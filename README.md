# APIexamples

This repository contains examples of API requests to ScanMeFindMe API.

For detailed description of commands and parameters refer to https://scanmefindme.com/en/api

Before you begin, set the API key:

```shell
export SCANMEFINDMEKEY="abcdef1234566789"
```

## Authentication examples

### Option 1 - add apikey to the query string

It is the easiest to pass an API key through a query string in a URL.
However, this method can risk API key exposure since, despite encryption,
the parameters can be stored in web server logs.

```shell
curl https://api.scanmefindme.com/v1/summary?apikey=$SCANMEFINDMEKEY
```

API key can be added to query string also in PUT and POST methods:

```shell
curl -X PUT --upload-file file.txt \
     https://api.scanmefindme.com/v1/uploadfile/file.txt?apikey=$SCANMEFINDMEKEY
```

```shell
curl -X POST \
     -d 'text=tel:1234567890123' \
     --output qrcode.png \
     https://api.scanmefindme.com/v1/create?apikey=$SCANMEFINDMEKEY
```

### Option 2 - add authentication header

```shell
curl -H "X-Api-Key: $SCANMEFINDMEKEY" \
     https://api.scanmefindme.com/v1/summary
```

or

```shell
curl -H "Authorization: Bearer $SCANMEFINDMEKEY" \
     https://api.scanmefindme.com/v1/summary
```

## Command /create

Generate static code to email support@scanmefindme.com (POST, application/x-www-form-urlencoded):
```shell
curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -d 'text=mailto:support@scanmefindme.com&template=shared/withcaption&t.caption=EMAIL+US&format=pdf&width=5cm&dpi=300' \
     --output qrcode.pdf \
     https://api.scanmefindme.com/v1/create
```

Generate static code to follow URL (POST, multipart/form-data):
```shell
curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -F 'text=https://scanmefindme.com' \
     -F 'template=shared/withcaption' \
     --output qrcode.png \
     https://api.scanmefindme.com/v1/create
```

Generate static code for the URL https://scanmefindme.com, save it (POST, JSON):
```shell
curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -H "Content-Type: application/json" \
     -d "{\"text\":\"https://scanmefindme.com\",\"format\":\"svg\",\"saveas\":\"Testing API\"}" \
     --output qrcode.svg \
     https://api.scanmefindme.com/v1/create
```

Generate static code for the URL https://scanmefindme.com (GET):
```shell
curl "https://api.scanmefindme.com/v1/create?apikey=$SCANMEFINDMEKEY&text=https%3A%2F%2Fscanmefindme.com" \
    > qrcode.png
```

Generate static code for contact card:
```shell
curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -d 'type=static.mecard&v.firstname=Mary&v.lastname=Jones&v.tel=7654321&template=shared/withmargin' \
     --output qrcode.png \
     https://api.scanmefindme.com/v1/create
```

Generate static code for calendar event:
```shell
curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -d 'type=static.vevent&v.summary=New+Year+party&v.dtstart=20211231T210000&v.duration=PT5H&template=shared/withmargin' \
     --output qrcode.png \
     https://api.scanmefindme.com/v1/create
```

Generate dynamic code for a URL:
```shell
curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -d 'type=url&v.url=https%3A%2F%2Fscanmefindme.com&template=shared/withmargin' \
     --output qrcode.png \
     https://api.scanmefindme.com/v1/create
```

Generate dynamic code for a contact:
```shell
curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -d 'type=contact&v.firstname=Mary&v.lastname=Jones&v.phone1=7654321&template=shared/withmargin' \
     --output qrcode.png \
     https://api.scanmefindme.com/v1/create
```

Generate dynamic code for a page (without files):

```shell
curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -F 'type=page' \
     -F 'v.heading=Hello' \
     -F 'v.description=Some more text' \
     --output qrcode.png \
     https://api.scanmefindme.com/v1/create
```

Generate dynamic code for a page (with files)

First, upload files using the [/uploadfile](#command-uploadfile) command. Assuming you uploaded
*hotellogo.png*, *microwave.pdf* and *coffeemaker.pdf*:

```shell
curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -F 'type=page' \
     -F 'v.heading=Welcome to our hotel' \
     -F 'v.description=Enjoy your stay, here you can find the appliances instructions:' \
     -F 'v.logo=hotellogo.png' \
     -F 'v.files=microwave.pdf' \
     -F 'v.files="coffeemaker.pdf;Coffee maker"' \
     -F 'template=shared/withmargin' \
     --output qrcode.png \
     https://api.scanmefindme.com/v1/create
```


## Command /uploadfile

Direct upload of small files:

```shell
curl -X PUT -H "X-Api-Key: $SCANMEFINDMEKEY" \
     --upload-file sample.png \
     https://api.scanmefindme.com/v1/uploadfile/sample.png
```

Two-step upload of files:

```shell
curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -F "files=sample.png" \
     -F "files=mypath/file.txt" \
     https://api.scanmefindme.com/v1/uploadfile
```

In case of JSON format "files" can be passed as a string (for a single file) or as an array
```shell
curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -H "Content-Type: application/json" \
     -d "{\"files\":[\"file1.txt\",\"file2.png\"]}" \
     https://api.scanmefindme.com/v1/uploadfile

curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -H "Content-Type: application/json" \
     -d "{\"files\":\"file3.txt\"}" \
     https://api.scanmefindme.com/v1/uploadfile
```

Example of POST request with application/x-www-form-urlencoded content-type:
```shell
curl -X POST -H "X-Api-Key: $SCANMEFINDMEKEY" \
     -d "files=sample.png&files=mypath/file.txt" \
     https://api.scanmefindme.com/v1/uploadfile
```

Same format when passing data in a query string
```shell
curl -X POST "https://api.scanmefindme.com/v1/uploadfile?apikey=$SCANMEFINDMEKEY&files=sample.png&files=mypath/file.txt"
```
