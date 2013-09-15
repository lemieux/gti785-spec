Transfer protocol specification
===============================

## General notes
### Variables
Elements like `{ID}` represents variables that will be replaced at runtime.  

#### Examples
**URL : ** `/music/{ID}/` needs a song ID to be valid.

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


### Output format
All returned output will be under this format :  

```xml
    <response>
        <data>
            ...
        </data>
        <extra-data>
            ...
        </extra-data>
    </response>
```

The `<data>` tag is used to return main data  (i.e, song list, etc.) and the `<extra-data>` tag is used to return extra information that your client and server will understand in the case you want to implement extra functionnality that might not be supported by other clients.


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
The expected output should be a list of MP3 metadata that is given by VLC4J.


#### Get one song
URL
:   `/music/{ID}/`

HTTP Method
:   `GET`