Transfer protocol specification
===============================

## General notes
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


### Listing songs
**URL :** /music/  
**HTTP Method :** GET  

#### Expected input
None

#### Expected output
The expected output should be a list of MP3 metadata that is given by VLC4J.

