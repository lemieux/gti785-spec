Transfer protocol specification
===============================

## General notes
### Variables
Elements like `{ID}` represents variables that will be replaced at runtime.  

#### Examples
URL
:   `/music/{ID}/` needs a song ID to be valid.

### REST
For convenience, all endpoints must comply to the REST convention. More information is available on [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer).  

In short, RESTful services build their URL this way :  

`/{OBJET_TYPE}/{ID}`

And the HTTP method define the action to be done.  

PUT (Create)
:   Create a new instance

GET (Read)
:   Fetch information

POST (Update)
:   Update an existing instance

DELETE (Delete)
:   Delete an existing instance


#### Examples

`GET /music/` will return all songs  
`GET /music/{ID}` will return one song according to the given ID  
`POST /music/{ID}` will update the given instance with the payload that is sent along.

#### Notes
Since this application is about getting information and sending commands, we should only use `GET` and `POST` on a limited set of URLs.

### Extra data
In the case you want to implemented extra stuff, you can add an `extra-data` parameter to your input or your output, so you can add data without polluting the standardized dataset.  

If you use it, you should use an application key to identify your application so our `extra-data` won't be mixed up. You can see an implementation of this principle down below.

### Input format
Most of the time, the client won't need to send anything to the server. 

```
POST (HTTP headers)
----
extra-data : {
    key: '{APPLICATION_KEY}',
    ...
}

```

### Output format
All returned output will be under this format :  

```xml
    <response>
        <data>
            ...
        </data>
        <extra-data key="{APPLICATION_KEY}">
            ...
        </extra-data>
    </response>
```

The `<data>` tag is used to return main data  (i.e, song list, etc.) and the `<extra-data>` tag is used to return extra information that your client and server will understand in the case you want to implement extra functionnality that might not be supported by other clients.


## Model representation

### Music
A song is represented by an id and the metadata returns by vlcj.

#### Example
TODO : Find an find a snippet of what the data looks like...

### Video
Not implemented yet.

#### Example
TODO : Find an find a snippet of what the data looks like...

## HTTP Endpoints
Here are all HTTP endpoints needed to communicate with the server with the complete details.


### Music

#### Listing songs
URL
:   `/music/`

HTTP Method
:    `GET`

##### Expected input
None

##### Expected output
The expected output should be a list of MP3 metadata that is given by vlcj.

###### Example
```xml
    <response>
        <data>
            <songs>
                <song id="{ID}">
                    {MUSIC MODEL REPRESENTATION}
                </song>
                <song>
                    ...
                </song>
                ...
            </songs>
        </data>
        <extra-data>
            ...
        </extra-data>
    </response>

```


#### Get one song
URL
:   `/music/{ID}/`

HTTP Method
:   `GET`

##### Expected input
None, all parameters are in the URL

##### Expected output
The expected output should be the representation of one song has given by vlcj.

###### Example
```xml
    <response>
        <data>
            <song id="{ID}">
                {MUSIC MODEL REPRESENTATION}
            </song>
        </data>
        <extra-data>
            ...
        </extra-data>
    </response>

```

### Video

#### Listing videos
URL
:   `/video/`

HTTP Method
:    `GET`

##### Expected input
None

##### Expected output
The expected output should be a list of video metadata that is given by vlcj.

###### Example
```xml
    <response>
        <data>
            <videos>
                <video id="{ID}">
                    {VIDEO MODEL REPRESENTATION}
                </video>
                <video>
                    ...
                </video>
                ...
            </videos>
        </data>
        <extra-data>
            ...
        </extra-data>
    </response>

```


#### Get one video
URL
:   `/video/{ID}/`

HTTP Method
:   `GET`

##### Expected input
None, all parameters are in the URL

##### Expected output
The expected output should be the representation of one video has given by vlcj.

###### Example
```xml
    <response>
        <data>
            <video id="{ID}">
                {VIDEO MODEL REPRESENTATION}
            </video>
        </data>
        <extra-data>
            ...
        </extra-data>
    </response>

```

### Player
The player is more a controller than a model, but it will look like a REST endpoint too.

#### Get the player status
URL
:   `/player/`

HTTP Method
:   `HEAD`