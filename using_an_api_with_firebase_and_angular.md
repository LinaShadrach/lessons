In the last lesson we covered how to make a simple API call. What if we want to use the data that is returned multiple times in multiple places in our app? It is inefficient to make an API call every time we want to use that data, as many APIs have limits. Instead, we should store the data in a database. In this lesson, we’ll walk through how to save data returned from an API call to Firebase.

Let’s continue to work with our Mars Rover Photos app that we created in the last lesson. We’ll modify our app such that we save all images returned by the API call to our database.

## Firebase _and_ Web API Configuration

To begin, use [this lesson](https://www.learnhowtoprogram.com/javascript/angular-extended/firebase-introduction-and-setup) as a guide to configure your app to use Firebase. Make sure not to remove any of the configuration we set up in the last lesson.

## Using a Model with API Data

The API call that we used in the last lesson returns a lot of information. We chose to focus on a few particular bits of data pertaining to each photograph to keep things simple:

* the image’s URL
* the name of the camera that took the photo
* the date the photo was taken

The easiest way to save all of this information to Firebase is to _encapsulate_ it in an object. To do this, we’ll create a model in the same way as we have before.

```
$ ng g class photo.model
```

In this model, we’ll declare our `Photo` object blueprint.

<div class="filename">src/app/photo.model.ts</div>
```
export class Photo {
  constructor(public imageURL: string, public camera: string, public date: string) {}
}
```

This allows us to import this model and make it available to any file in our project.

## Subscribing

Next, we’ll add a button to the html in this component to give users the option to save the images returned from their API call.

<div class="filename">src/app/rover-form/rover-form.component.html</div>
```html
…
    </select>
  </div>
  <button (click)="getRoverImages(roverDate.value, roverCamera.value)">View Images</button>
  <button (click)="saveRoverImages(roverDate.value, roverCamera.value)">Save All Images</button>
</form>
```

This button triggers the method `saveRoverImages()`, which we need to add to the `ts` file for this component.

<div class="filename">src/app/rover-form/rover-form.component.ts</div>
```
…
  saveRoverImages(date, camera){
    this.marsRoverPhotos.saveImages(date, camera);
    alert(`The images from ${date} taken by the ${camera} camera have been saved to the database.`)
  }
…
```

As with our `getByDateAndCamera()` method, this method references the private instance of `MarsPhotoService` that we invoke. We call the method `saveImages()` on it, which we need to add to our service.

<div class="filename">src/app/mars-rover-api-photos.service.ts</div>
```
...
import { Photo } from './photo.model';
import { PhotoService } from './photo.service';
...
export class MarsRoverApiPhotos{
   constructor(private http: HTTP, private photoService: PhotoService) { }
...
saveImages(date: string, camera: string) {
  return this.http.get(`https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?earth_date=${date}&camera=${camera}&api_key=${marsRoverKey}`)
    .subscribe(response => {
      let foundPhoto: Photo;
      for(let image of response.json().photos) {
        foundPhoto = new Photo(image.img_src, camera, date);
        this.photoService.addPhoto(foundPhoto);
      }
    });
}
...
```

The logic above is not too different than what we wrote in the method `getByDateAndCamera()`. However, instead of just _returning_ the response, we are _subscribing_ to it. If you did not read the [optional lesson on subscribing](https://www.learnhowtoprogram.com/javascript/angular-extended/optional-subscribing-to-observables), please read it now. We need to subscribe to our API call so that we can have access to the individual properties of our response object. Otherwise, we would not be able to create a new `Photo` object with the properties our model requires. Since we're using the `Photo` model, we need to import it. By examining the structure of the response object, we can see that the data we need is contained in an array named `photos`. The `for` loop iterates over each of the items in this array. Then, for each item, a new `Photo` is created using our model, and we use our `PhotoService` (which we will create shortly) to add this photo to the database. Note that `response.photos` is undefined outside of the `subscribe` block. This is why the line `this.photoService.addPhoto(foundPhoto)` must be located within this block.

## Using a Service in Another Service

You might have noticed that we try to use `PhotoService` without providing it to this service. In Angular, when we want a method in one service (the parent service) to reference another service (the child service), we add an instance of the child service to the list of providers for _all_ of the components that are referencing that method from the parent service. These components will still list the parent service as a provider, as well. In our case, we want the `RoverFormComponent` to be able to use the method `saveImages()` in `MarsRoverApiPhotos`, which uses the `addPhoto()` method in `PhotoService`. `MarsRoverApiPhotos` is the parent service, `PhotoService` is the child service, and `RoverFormComponent` is the component that uses these services. This means that we should import `PhotoService` and add it to our list of providers in `RoverFormComponent`.

<div class="filename">src/app/rover-form/rover-form.component.ts</div>
```
import { PhotoService } from '../photo.service';
...
@Component({
  selector: 'app-rover-form',
  templateUrl: './rover-form.component.html',
  styleUrls: [ './rover-form.component.css' ],
  providers: [ MarsRoverApiPhotos, PhotoService ]
})
```

`PhotoService` will now be available to not only `RoverFormComponents` but also all of its child components. If we want to use `PhotoService` in `PhotosListComponent`, we still need to add it to the list of imports and to the constructor to create a new instance of the service, but we _do not_ have to add it to our list of providers.

Now that we know how to inject one service into another, let’s create our `PhotoService` and declare the method `addPhoto()`, which we’ll use to save images to Firebase.

## Firebase Services

```
ng g service photo
```

The following code should be familiar from our previous work with Firebase.

<div class="filename">src/app/photo.service.ts</div>
```typescript
import { Injectable } from '@angular/core';
import { Photo } from './photo.model';
import { AngularFireDatabase, FirebaseListObservable } from 'angularfire2/database';

@Injectable()
export class PhotoService {
  photos: FirebaseListObservable<any[]>;

  constructor(private af: AngularFireDatabase) {
    this.photos = af.list('photos');
  }
  addPhoto(newPhoto: Photo) {
    this.photos.push(newPhoto);
  }
}
```

If we serve up our app and submit our form with the `Save Images` button, we should get see the alert we set up. We should also be able to see our newly saved data in Firebase.

It’s important to know how to save data from an API call directly to a database. However, it’s very common for an app to allow a user to choose what data to save.

## Saving User Chosen API Data

The process outlined below should look familiar to you. It’s a simple demonstration of how to  save data to Firebase with the extra step of retrieving data from an API first.

Our goal is to allow users to save an image so that they can view it again later. Users should also be able to delete individual images from the list of the saved images.

We want our users to be able to click a “Save” button for each of the photos return by the API call and have that button trigger the `addPhoto()` method in our service. Let’s add a _Save_ button next to each of the images.

<div class="filename">src/app/photos-list/photos-list.component.html</div>
```
<h1>Martian Rover Photos:</h1>
<h2> From {{childPhotos.photos[0].earth_date}} by the {{childPhotos.photos[0].camera.name}}</h2>
<div *ngFor="let photo of childPhotos.photos">
  <img src = {{photo.img_src}}/><br>
  <button (click)="saveImage(photo.img_src, childPhotos.photos[0].camera.name, childPhotos.photos[0].earth_date)">Save Photo</button>
</div>
```

We’ve added an event binder to the button that calls the method `saveImage()` when the button is clicked. Since we want to save the date and camera associated with each image the user chooses to save, we’ll include these values in our list of arguments.

Next, we need to add the corresponding method to our back-end file. Since our _Save_ button resides in the `photos-list` component, we’ll add the logic for the method to _photos-list.ts_.

<div class="filename">src/app/photos-list/photos-list.component.ts</div>
```
import { Component, Input } from '@angular/core';
import { PhotoService } from '../photo.service';
import { Photo } from '../photo.model';

@Component({
  selector: 'app-photos-list',
  templateUrl: './photos-list.component.html',
  styleUrls: [ './photos-list.component.css' ],
  providers: [ PhotoService ]
})

export class PhotosListComponent {
  @Input() childPhotos;
  constructor(private photoService: PhotoService) { }
  saveImage(imgURL: string, camera: string, date: string) {
    let newPhoto: Photo = new Photo(imgURL, camera, date);
    this.photoService.addPhoto(newPhoto);
    alert('This image has been added to your list of saved images.');
  }
}
```

## Displaying Saved Data

We need to create a spot for our users to view the images they’ve saved, so we’ll create another component, as well as a new route for this component.

```
$ ng g component user-photos-list
```

We’ll need to import this component into _app.routing.ts_ and add a route to our _appRoutes_ array.

```
…
import { UserPhotosListComponent } from './user-photos-list/user-photos-list.component';
…
  {
    path: 'user/photos',
    component: UserPhotosListComponent
  }
…
```

Before we forget, let’s add a `routerLink` that let’s the user navigate to the `UserPhotosListComponent` from the `RoverFormComponent`.

<div class="filename">src/app/rover-form/rover-form.component.html</div>
```
<h1>Curiousity Images</h1>
<a routerLink="user/photos">View Your Saved Photos</a>
...
```

For each image, we’ll include a button for deleting it. Let’s add code to our _html_ now. We also need to make sure to include a link back to the `RoverFormComponent` so that the user can easily navigate around our site.

<div class="filename">src/app/user-photos-list/user-photos-list.component.html</div>
```
<a routerLink=''>Back to Search Page</a>
<h1>Your Saved Photos:</h1>
<div *ngFor="let photo of savedPhotos | async">
  <img src = {{photo.imageURL}} class=”photo”><br>
  <p>{{photo.date}} | {{photo.camera}}</p>
  <button (click)="deletePhoto(photo)">Delete Photo</button>
</div>
```

Let’s add the same styling to these images as we did to the images in our `PhotoListComponent`.

<div class="filename">src/app/user-photos-list/user-photos-list.component.css</div>
```
img {
  height: 500px;
  width: 500px;
  padding: 1em;
  border: 5px solid brown;
}

.photo {
  object-fit: cover;
}
```

Now we need to create the method `deletePhoto()` in the back-end, as well as set up the logic in `ngOnInit()` so that the list of saved photos will be ready to use when the component is rendered.

<div class="filename">src/app/user-photos-list/user-photos-list.component.ts</div>
```
import { Component, OnInit } from '@angular/core';
import { PhotoService } from '../photo.service';
import { Photo } from '../photo.model';
import { AngularFireDatabase, FirebaseListObservable } from 'angularfire2/database';

@Component({
  selector: 'app-user-photos-list',
  templateUrl: '/user-photos-list.component.html',
  styleUrls: [ './user-photos-list.component.css' ],
  providers: [ PhotoService ]
})

export class UserPhotosListComponent implements OnInit {
  constructor(private photoService: PhotoService) { }
  savedPhotos: FirebaseListObservable <any[]> = null;
  ngOnInit(){
    this.savedPhotos = this.photoService.getPhotos();
  }
  deletePhoto(selectedPhoto: Photo) {
    this.photoService.deletePhoto(selectedPhoto);
    alert("This image has been deleted from your list of saved images.");
  }
}
```

As usual, we’ve included `PhotoService` in the list of providers for this component and a private instance of it in the list of properties for this class. This allows us to call the methods `getPhotos()` and `deletePhoto()` that we are planning to add to our `PhotoService`. Let’s add those methods now.

<div class="filename">src/app/photo.service.ts</div>
```typescript
...
  getPhotos() {
    return this.photos;
  }
  deletePhoto(selectedPhoto) {
    let foundPhoto = this.getPhotoById(selectedPhoto.$key);
    foundPhoto.remove();
  }
  getPhotoById(photoId: string){
    return this.af.object('photos/' + photoId);
  }
...
```

In addition, we have added the `getPhotoById()`, since the `deletePhoto()` method depends on it.

At this point, our app allows the user to save all images returned from an API call, save and delete particular images to/from Firebase, and view the images that they have saved.

[Mars Rover repo](https://github.com/epicodus-lessons/mars-rover).
