In this lesson, we'll build a simple application that pulls information from one of NASA's Open APIs. We’ll start off by reviewing the basics of making API calls. Then we’ll cover how to make an API call in Angular and how to display the response data to the user.

## Registering for an API Key

First, we need to register for an API key by visiting [this page](https://api.nasa.gov/api.html#authentication). After entering your information, you should be redirected immediately to a page that lists your API key. Not all API keys are returned so promptly; sometimes it will take an organization days to respond. It's important to consider how long it will take to get an API key when planning our projects.

## Experimenting with API Calls

Next, let's try out an API request in Postman. If you did not read [this lesson](https://www.learnhowtoprogram.com/lessons/testing-api-calls-with-postman) introducing Postman, read through it now. Here you can enter your API key, build sample requests with multiple parameters, and see the JSON data they return.

We'll experiment with the [Mars Rover Photos API](https://api.nasa.gov/api.html#MarsPhotos). Following the example listed in the documentation, let's construct an API call and test it in Postman. Make sure to replace `DEMO_KEY` with your own API key. Remember, you can click the `Params` button and add `api_key` as a key and your NASA API key as the value to keep things organized.

```
https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?earth_date=2017-01-01&camera=mast&api_key=DEMO_KEY
```

When we click `Send`, a `GET` request is made to the URL we provide, and JSON-formatted data is returned in the response body:

```
{
  "photos": [
    {
      "id": 605121,
      "sol": 1566,
      "camera": {
        "id": 22,
        "name": "MAST",
        "rover_id": 5,
        "full_name": "Mast Camera"
      },
      "img_src": "http://mars.jpl.nasa.gov/msl-raw-images/msss/01566/mcam/1566MR0079890140800214E01_DXXX.jpg",
      "earth_date": "2017-01-01",
      "rover": {
        "id": 5,
        "name": "Curiosity",
        "landing_date": "2012-08-06",
        "launch_date": "2011-11-26",
        "status": "active",
        "max_sol": 1737,
        "max_date": "2017-06-25",
        "total_photos": 316990,
        "cameras": [
          {
            "name": "FHAZ",
            "full_name": "Front Hazard Avoidance Camera"
          },
          {
            "name": "NAVCAM",
            "full_name": "Navigation Camera"
          },
          {
            "name": "MAST",
            "full_name": "Mast Camera"
          },
          {
            "name": "CHEMCAM",
            "full_name": "Chemistry and Camera Complex"
          },
          {
            "name": "MAHLI",
            "full_name": "Mars Hand Lens Imager"
          },
          {
            "name": "MARDI",
            "full_name": "Mars Descent Imager"
          },
          {
            "name": "RHAZ",
            "full_name": "Rear Hazard Avoidance Camera"
          }
        ]
      }
    },
    {
      "id": 605122,
      "sol": 1566,
      "camera": {
        "id": 22,
        "name": "MAST",
        "rover_id": 5,
        "full_name": "Mast Camera"
      },
      "img_src": "http://mars.jpl.nasa.gov/msl-raw-images/msss/01566/mcam/1566MR0079890130800213D01_DXXX.jpg",
      "earth_date": "2017-01-01",
      "rover": {
        "id": 5,
        "name": "Curiosity",
        "landing_date": "2012-08-06",
        "launch_date": "2011-11-26",
        "status": "active",
        "max_sol": 1737,
        "max_date": "2017-06-25",
        "total_photos": 316990,
        "cameras": [
          {
            "name": "FHAZ",
            "full_name": "Front Hazard Avoidance Camera"
          },
          {
            "name": "NAVCAM",
            "full_name": "Navigation Camera"
          },
          {
            "name": "MAST",
            "full_name": "Mast Camera"
          },
          {
            "name": "CHEMCAM",
            "full_name": "Chemistry and Camera Complex"
          },
          {
            "name": "MAHLI",
            "full_name": "Mars Hand Lens Imager"
          },
          {
            "name": "MARDI",
            "full_name": "Mars Descent Imager"
          },
          {
            "name": "RHAZ",
            "full_name": "Rear Hazard Avoidance Camera"
          }
        ]
      }
    },
...

```

_Note: To view JSON in Chrome nicely, [add the JSONView extension](https://chrome.google.com/webstore/search/jsonview?hl=en-US), or use a site like [JSON Pretty Print](http://jsonprettyprint.com/)._

In addition to our `api_key` query parameter, we also added the `earth_date` and `camera` parameters. This narrows our search to a particular camera and a particular day. As you can see, there’s still more information than just an image URL in the response body. We will address how to access this data shortly.

## Implementing API Data in Angular

Let's create an Angular CLI app to work with the data we receive from this API call:

```
$ ng new mars-rover
```

This project will use a router, so make sure configure your app for routing and to add the appropriate setup code to _app.component.html_. Use [this lesson](https://www.learnhowtoprogram.com/javascript/angular-extended/implementing-a-router) as a guide.

## Using a Service with API Data

Next, we need to create a service to contain our API call. To review services, checkout [this lesson](https://www.learnhowtoprogram.com/javascript/angular-extended/services). We want all of our API calls in services so that the data they retrieve is accessible throughout our projects. Each API we use should have a separate service to keep things organized.

```
$ ng g service mars-rover-api-photos
```

This creates two files. As before, we will ignore the test file. First, let's make sure the service file has all the imports we will use.

<div class="filename">src/app/mars-rover-api-photos.service.ts</div>
```
import { Injectable } from '@angular/core';
import { Http, Response } from '@angular/http';
import { Observable } from 'rxjs/Observable';

@Injectable()
export class MarsRoverApiPhotos {
  constructor() { }
}
...
```

As covered in the lessons on [dependency injection](https://www.learnhowtoprogram.com/javascript/angular-extended/dependency-injection) and [services](https://www.learnhowtoprogram.com/javascript/angular-extended/services), the `Injectable` import allows us to use the decorator `@Injectable`, which makes our service usable by our other files.

The `Http` and `Response` imports gives us access to Angular's built in http service. In order to use this service, it must be included in our list of imports in _app.module.ts_. Luckily, Angular CLI does this by default.

<div class="filename">app.module.ts</div>
```typescript
import { HttpModule } from '@angular/http';
…

@NgModule({
  declarations: [
    …
  ],
  imports: [
    HttpModule,
    …
  ],
  …
})
…
```

The `Observable` import lets us treat the data returned from API calls as Observables. If you did not read the [lesson on Observables](https://www.learnhowtoprogram.com/javascript/angular-extended/subscribing-to-observables-a5e7e027-4759-42ce-bac9-ecc15113290f), go back and read it now.

Now, we'll add a parameter to our constructor:

<div class="filename">src/app/mars-rover-api-photos.service.ts</div>
```
...
export class MarsRoverApiPhotos {
  constructor(private http: Http) { }
}
...
```

This declares that our service uses Angular's `Http` service and will refer to it as `http`. We have declared that we want this property to be `private`. We want this to be the only class that has access to this particular instance of the `Http` service. This helps keep our code compartmentalized.

We need to add a method to this class that utilizes the http service to make an API call. This method will simply return the results of the API call. We'll plan to let users choose which date and which camera they would like to view the photos from, so we'll declare it with two parameters.

<div class="filename">src/app/mars-rover-api-photos.service.ts</div>
```
...
getByDateAndCamera(date: string, camera: string) {
  return this.http.get(`https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?earth_date=${date}&camera=${camera}&&api_key=DEMO_KEY`)
}
...
```

We call the `get()` method on our http object. This method is part of [Angular's http service](https://docs.angularjs.org/api/ng/service/$http). Other methods in the http service include `post()`, `patch()`, and `delete()`. The method signature for `get()` is `get(url: string, options?: RequestOptionsArgs): Observable<Response>;`, indicating that this method takes a url as its first argument, can optionally take a second argument (which we will not worry about), and returns the response as an `Observable`.

## Gathering User Data

Now that we have a service that houses our API call, let's create a component that will use our service. This component will contain our form and logic for gathering values for the `camera` and `sol` parameters.:

```
$ ng g component rover-form
```

Don’t forget to check that the component has been added to your list of imports at the top of _app.module.ts_, and to the `declarations` array listed under `@NgModule`. Before we add content to this component, let's create a new path for it in _app.routing.ts_:

<div class="filename">src/app/app.routing.ts</div>
```
import { RoverFormComponent } from './rover-form/rover-form.component';
....
{
  path: '',
  component: RoverFormComponent
}
```

Now, let's add our form:

<div class="filename">src/app/rover-form.component/rover-form.component.html</div>
```
<h1>Curiosity Images</h1>

<form>
  <div class="form-group">
    <label for="camera">Date</label>
    <input type=date #roverDate>
  </div>
  <div class="form-group">
    <label for="camera">Camera</label>
    <select #roverCamera>
      <option value="FHAZ">Front Hazard Avoidance Camera</option>
      <option  value="RHAZ">Rear Hazard Avoidance Camera</option>
      <option value="MAST">Mast Camera</option>
      <option  value="CHEMCAM">Chemistry and Camera Complex</option>
      <option  value="MAHLI">Mars Hand Lens Imager</option>
    </select>
  </div>
  <button (click)="getRoverImages(roverDate.value, roverCamera.value)">Get Photos</button>
</form>
```

Our button has a `click` event binder attached to it that triggers `getRoverImages()`. We need to add this method to our `rover-form` component. This method will collect the date and camera selection from the form and pass it to our service file. It will also set the value for `this.photos` to the response we get from our API. This response comes back as an `Observable`, much like the `FirebaseObservable`s we have worked with in the past.

<div class="filename">src/app/rover-form.component/rover-form.component.ts</div>
```
import { Component } from '@angular/core';
import { Observable } from 'rxjs/Observable';
import { MarsRoverApiPhotos } from '../mars-rover-api-photos.service';

@Component({
  selector: 'app-rover-form',
  templateUrl: './rover-form.component.html',
  styleUrls: [ './rover-form.component.css' ],
  providers: [ MarsRoverApiPhotos ]
})

export class RoverFormComponent {
  photos: any[]=null;
  constructor(private marsRoverPhotos: MarsRoverApiPhotos) { }
  getRoverImages(date: string, camera: string) {
    this.marsRoverPhotos.getByDateAndCamera(date, camera).subscribe(response => {
        this.photos = response.json();
    });
  }
}
```

`this.marsRoverPhotos.getByDateAndCamera(date, camera)` returns an `Observable`, so we call the `subscribe()` method on it. This allows us to call the `json()` method on the response to cast the it as a Javascript object.  By doing this, we can render our results in the browser _without_ using an async pipe. (The async pipe is useful, but some developers feel that it clutters up the template. We'll leave it up to you to choose which to use.) We use the fat arrow so `this` remains scoped to our class so we can update the `photos` property. Notice that we declare an instance of the service `marsRoverPhotos` in our constructor in the same way we did for our other services.

## Displaying Data From an API Call

We still need a spot for displaying the rover photos. Let's create another component for that. After that, we'll create a model for our `Photo` object.

```
$ ng g component photos-list
```

Instead of routing to this component after the user submits the form, `photos-list` will be a child component of `rover-form`. This means we need to update our _rover-form.component.html_. Add the following code to the bottom of this file.

<div class="filename">src/app/rover-form.component/rover-form.component.html</div>
```
...
<div *ngIf="photos">
  <app-photos-list [childPhotos]="photos"></app-photos-list>
</div>
...
```

We have declared a property binding between `childPhotos`, a property we'll declare in our child component, and `photos`, the parent component property that we will store our array of photos returned from the api call. This way, `PhotosListComponent` will have access to the photos returned by the API call. We use an `ngIf` directive so that the child component only shows after the form is submitted.

Now, let's add code to the child component  to display the results of our API call.

<div class="filename">src/app/photos-list.component/photos-list.component.ts</div>
```
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-photos-list',
  templateUrl: './photos-list.component.html',
  styleUrls: [ './photos-list.component.css' ],
  providers: [ ]
})

export class PhotosListComponent {
  @Input() childPhotos;
  constructor() { }
  ...
```

We've add the `@Input` Decorator to capture the `photos` array passed from the parent component.

In the template, we simply loop through the list of photos by targeting the `img_src` property:

<div class="filename">src/app/photos-list/photos-list.html</div>
```
<h1>Martian Rover Photos:</h1>
<h2> From {{childPhotos.photos[0].earth_date}} by the {{childPhotos.photos[0].camera.name}}</h2>
<div *ngFor="let photo of childPhotos.photos;" class="photo">
  <img src = {{photo.img_src}}/>
</div>
```

Great! When we click `Get Photos` our `photos-list` component will render.

## Styling our Images

Our pages looks pretty messy without any formatting on our images. Let’s add some basic styling to _photos-list.component.css_ before building out any more features.

<div class="filename">src/app/photos-list.component/photos-list.component.css</div>
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

Much better!

## Managing Unruly API Data

It’s very uncommon for the data returned by an API call to be in perfect condition. If you play around with this API, you’ll notice that not all cameras take pictures everyday. Let’s make our project more user-friendly by adding a message to display to users when no pictures were found for their search query. This also prevents our app from logging a nasty error when it tries to render the `PhotosListComponent` for a query that does not return any photos:

```
>> ERROR TypeError: Cannot read property 'earth_date' of undefined
    at Object.eval [as updateRenderer] (PhotosListComponent.html:2)
    at Object.debugUpdateRenderer [as updateRenderer] (core.es5.js:13144)
    at checkAndUpdateView (core.es5.js:12293)
    at callViewAction (core.es5.js:12651)
    at execComponentViewsAction (core.es5.js:12583)
    at checkAndUpdateView (core.es5.js:12294)
    at callViewAction (core.es5.js:12651)
    at execEmbeddedViewsAction (core.es5.js:12609)
    at checkAndUpdateView (core.es5.js:12289)
    at callViewAction (core.es5.js:12651)

>> ERROR CONTEXT DebugContext_ {view: Object, nodeIndex: 4, nodeDef: Object, elDef: Object, elView: Object}
```

To get rid of this error, all we need to do is set `this.photos` to null before making the call to the service, and add an `if` statement that checks the length of photos array in the response.


<div class="filename">src/app/rover-form.component/rover-form.component.ts</div>
```
...
export class RoverFormComponent {
  photos: any[];
  noPhotos: boolean=false;
  constructor(private marsRoverPhotos: MarsRoverApiPhotos) { }

  getRoverImages(date: string, camera: string) {
    this.photos=null;
    this.marsRoverPhotos.getByDateAndCamera(date, camera).subscribe(response => {
      if(response.json().photos.length > 0) {
        this.photos = response.json();
      }
    });
  }
}
```

By setting `this.photos` to `null`, we ensure that the `PhotosList` will only render if photos were found for the most recent search.

Next let's add our message:

<div class="filename">src/app/rover-form.component/rover-form.component.html</div>
```html
…
<div *ngIf="photos">
  <app-photos-list [childPhotos]="photos"></app-photos-list>
</div>
<div *ngIf="photos===null">
    <h3>There were no photos taken on {{roverDate.value}} by the {{roverCamera.value}} camera.</h3>
</div>
...
```

Note that we have also modified the attributes of the first `div`. The `if` statement we added makes it so that the message will only show when `photos` is null.

##  Storing API keys

There is still one last thing to fix: we need to remove the reference to `DEMO_KEY` and use the unique API key that we requested from NASA Open APIs. We do not want other developers using our key. We’ve already seen how to go about hiding our keys in an Angular app when we configured our app for Firebase. We created a file called _api-keys.ts_ in the _app_ folder. We will store all of our API keys in this file. We must also make sure to add it to our _.gitignore_ file. Add this file with the following code:

<div class="filename">src/app/api-keys.ts</div>
```
export const marsRoverKey = "{YOUR_KEY_HERE}"
```

We'll need to import the key into our service and alter the url string. Our updated service should look like:

<div class="filename">src/app/mars-rover-api-photos.service.ts</div>
```
import { marsRoverKey } from './api-keys';
import { Injectable } from '@angular/core';
import { Http, Response } from '@angular/http';
import { Observable } from 'rxjs/Observable';

@Injectable()
export class MarsRoverAPIPhotos {
  constructor(private http: Http) { }

  getByDateAndCamera(date: string, camera: string) {
    return this.http.get(`https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?earth_date=${date}&camera=${camera}&api_key=${marsRoverKey}`);
  }
}
```

Now, when we submit the form in our `rover-form` component, the component will grab the query parameter values from the form, pass these values to the proper method in our service, then render the child component with the results.  

### API Standards

As a side note, know that all APIs are different. Some use JSON in their response bodies, others use XML, and some may use different formats entirely. Also, not all APIs are set up to receive requests from web browsers, as the most common way to access an API is from a web server. Some APIs use the [CORS](http://enable-cors.org/)(Cross-Origin Resource Sharing) standard to enable browsers to use it (a good explanation of what CORS is can be found [here](https://livebook.manning.com/#!/book/cors-in-action/chapter-1/8); some other APIs use the [JSONP](http://dev.socrata.com/docs/cors-and-jsonp.html) approach for web browsers; and some APIs don't support web browser access at all.
