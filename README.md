# OpenTripPlanner Docker Install - Newark
Docker setup for a Newark OpenTripPlanner instance, including OpenStreetMap and GTFS data

## How to setup (Mac OS X)
Clone the repo, cd into the directory of the Dockerfile you'd like to use, and run
```
docker-machine create -d virtualbox --virtualbox-memory 8192 newark-otp
eval $(docker-machine env newark-otp)
docker build -t newark-otp .
```

## Running the Docker Container
Then, after it finishes, first run `docker-machine ip newark-otp` to get
the IP address for the container, and then run
`docker run -p 80:8080 newark-otp --router newark --server`

Now you should be able to access it in your browser at that IP address.

You can also run with the `--analyst` option to use OTP Analyst features, or run
without any optional arguments to see all available command line options.

## How to setup (Linux)
Similar to the OSX instructions, but without the extra docker-machine steps at the beginning.
```
docker build -t newark-otp .
docker run -p 80:8080 newark-otp --router newark --server
```

## Scripting with Data Volumes

In order to sync data and scripts from your host machine to the Docker container
for the scripting API, you'll need to just add pick a directory on your host machine,
and sync it to `var/otp/scripting` on the container.

Jython has already been added to the classpath by default in the Dockerfile, so it
will be enabled for scripting through either the `--script` or
`--enableScriptingWebService` options.

**Example:** `docker run -v /host/machine/dir:/var/otp/scripting`

### Syncing Data
In your script file, if you refer to any synced data (i.e. a population data CSV
for transportation analysis), you'll need to refer to the full file path on the
Docker container and not your host machine.

**Example:** `otp.loadCSVPopulation("/Users/me/test.csv")` becomes `otp.loadCSVPopulation("/var/otp/scripting/test.csv")`

The output file will show up in your host machine at the location of the synced volume
as well as in the location in the container.

Here is a full example of running this command against the container:

`docker run -v /Users/me:/var/otp/scripting newark-otp --router newark --script /var/otp/scripting/script.py`
