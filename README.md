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

This format will be used to illustrate a HTTP call and its [payload](http://en.wikipedia.org/wiki/POST_(HTTP)#Posting_data). Everything below `------` should be its `form data`.


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

#### Serialization format
By default, the exchange format will be `xml` since the information returned by vlcj is already in this format.  

You could perform a `OPTIONS` request on any endpoint to see what serialization type is supported by the server by checking its response.

##### Output example
The output will be text based and easy to decode.

```
accept-format:xml,json
application-key:XXXXXXXXXXXXX
extra-features:aaa,bbb,ccc,...
```
The `extra-features` should be a list of extra features your server implement. The `application-key` is the key to use when looking for `extra-data` in the response. That way, everyone could try to be compatible with others.


### Status code
All successful call will result in a `HTTP 200` for the response status code. If a resource is unavailable or doesn't exist (i.e, fetching a song with the wrong id, or trying to play a song that doesn't exist), the server should answer with a `HTTP 404`. For other errors, the server should return a `HTTP 500`. You can return extra data about the error in the `extra-data` part of your response if you want, but the HTTP status should be enough to identify what type of error you are facing (maybe not `HTTP 500`, but it could be anything and you should check your server, it should not happen).

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
The player is more a controller than a model, but it will look like a REST endpoint too. This spec won't specify the behavior your server is supposed to have. For example, if you choose that *playing* a media clears the playlist, that's your choice. Another team could choose to just jump to the song in the current playlist if found in it.

#### Get the player status
URL
:   `/player/`

HTTP Method
:   `GET`

##### Expected input
None

##### Expected output
This should be the current song or video that is playing plus information about time left and what is the next item in the playlist if any. It should be empty if not playing.

##### Example

```xml
    <response>
        <data>
            <song id="{ID}">
                {MUSIC MODEL REPRESENTATION}
            </song>
            OR
            <video id="{ID}">
                {VIDEO MODEL REPRESENTATION}
            </video>
            OR EMPTY
            <play-info>
                <media-length>HH:MM:SS</media-length> // length of the media being played
                <play-time>HH:MM:SS</play-time> // what part the player is reading
                <next-media>
                    <type>VIDEO/SONG</type>
                    <id>{ID}</id>
                </next-media>
            </play-info>
        </data>
        <extra-data>
            ...
        </extra-data>
    </response>
```

##### Notes
You could poll this endpoint periodically to synchronize your player with the server. For example, if you want to display a seek bar and you want it to be in sync, just fetch the `play-time` and adjust your seek bar in consequence, but don't forget to take response time in account. It should be different when we will read the actual stream because it should provide a set of accurate information about that.

#### Play a media
URL
:   `/player/`

HTTP Method
:   `POST`

##### Expected input
The expected input must contain the action to execute (`play`), the type, and the id of the media to be played.

###### Example
```
POST
----
action: play
type: video
id: 1
```

##### Expected output
None.

#### Stop the player
URL
:   `/player/`

HTTP Method
:   `POST`

##### Expected input
The expected input must contain the action to execute (`stop`).

##### Expected output
None.

#### Play previous/next media
URL
:   `/player/`

HTTP Method
:   `POST`

##### Expected input
The expected input must contain the action to execute (`previous` / `next`).

##### Expected output
The expected output should be the representation of one media has given by vlcj.

##### Example

```xml
    <response>
        <data>
            <song id="{ID}">
                {MUSIC MODEL REPRESENTATION}
            </song>
            OR
            <video id="{ID}">
                {VIDEO MODEL REPRESENTATION}
            </video>
        </data>
        <extra-data>
            ...
        </extra-data>
    </response>
```