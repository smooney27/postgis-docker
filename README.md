# Docker-ACMT
**Docker-ACMT** is three things:

1) A Flask REST API for PostGIS TIGER/Line geocoder using Docker containers. 
2) A Proxy file downloader to get shapefiles and data files to enable ACMT R code
3) The ACMT R code

Builds off of https://github.com/uwrit/postgis-docker.

To set up the Docker ACMT, you should:

1) Follow the Postgis-Docker instructions below, which will both install the Postgis geocoder and download files the ACMT needs
2) Load and run TestDockerACMT.R

Contact Steve Mooney (sjm2186@uw.edu) with any questions

# Postgis-Docker Instructions

1) Incorporates steps described in https://experimentalcraft.wordpress.com/2017/11/01/how-to-make-a-postgis-tiger-geocoder-in-less-than-5-days/ for setting up a PostGIS database with TIGER Geocoder, but does so in a pre-configured Docker container for simple setup.
2) Sets up a simple Python Flask REST API in a second Docker container as a wrapper for the database, binding to port 5000.

## Installation
Setting up PostGIS and loading [US Census TIGER spatial files](https://www.census.gov/programs-surveys/geography.html) can be a pain, with differing setup configurations for Windows and Unix systems, and the awkward necessity of executing SQL statements in PostGRES which output (somewhat error-prone) shell scripts, which in turn must be executed in a very specific order.

**postgis-docker** simplifies the process.
> **These steps assume you already have Docker installed on your computer.** If you don't have Docker installed, install [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) if you're on Windows, `brew cask install docker` if on a Mac, or `apt-get`/`yum` if Linux (the setup varies a bit by Linux distro, so search for instructions appropriate for you).

1) **clone the repo:**
```bash
$ git clone https://github.com/smooney27/docker-acmt.git
```

2) **Configure the `.env` file in the root directory to set the states to download geocoding files for**
```bash
$ cd docker-acmt
```

Edit the .env file that's there (use a text editor: Notepad on Windows, TextEdit on a Mac, vi on Linux, etc.) to include the states your addresses might be in:
```bash
GEOCODER_STATES=<Your state abbreviations (e.g. WA,OR,CA)>
```

3) **Finally:**
```bash
$ docker-compose up
```

And that's it! Note that that TIGER file-load process may take a while, depending on the US states you configure. The logic for dynamically loading and configuring the TIGER files is in [load_data.sh](./src/db/load_data.sh), which is a script adapted from the PostGIS default TIGER setup scripts, but made reusable and dynamic.

After setup is complete, test it out!
```bash
$ # Ex.: "1410 NE Campus Parkway, Seattle, WA 98195" (the University of Washington)
$ curl http://localhost:5000/latlong?q=1410+NE+Campus+Parkway%2c+Seattle%2c+WA+98195
{
  "building": 1410,
  "city": "Seattle",
  "lat": 47.6563,
  "long": -122.31314,
  "state": "WA",
  "street": "Campus",
  "streetType": "Pkwy",
  "zip": "98195"
}
```

The PostGIS and API containers can be taken down anytime with:
```bash
$ docker-compose down
```


