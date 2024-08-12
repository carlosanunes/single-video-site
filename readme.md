# Introduction

The problem I wanted to solve was how to share a (single) video without posting it to a streaming service or similar site, and without it being to easy for people to just save it, while still being easy for people to view it. After some searching I found multiple solutions to self host youtube clones, or similar video content management sites (e.g.: https://mediagoblin.org/, MediaCMS, Tube, etc.), but these were overkill. Ended up choosing a very light http server made for static websites (https://static-web-server.net/), Traefik to work as reverse proxy to provide HTTPS by leveragin Let's Enctrypt and the HLS protocol for video streaming. And in order for this to be easy to install and manage, docker contains for installation.

This solution is so light it runs from a Raspberry Pi Zero.

Dependencies:

- Docker
- FFMPEG (just to prepare the video, not necessary in normal running)

# Instructions

## Download the project files

Just clone the repo to a convenient location.

## Prepare the video for streaming

Convert the original video so that it is supported by the HLS standard using ffmpeg. And move the resulting files to the data folder. Take note that it is expected that the resulting m3u8 playlist be named 'index.m3u8'. 

E.g.: ffmpeg  -i video.mp4 -codec: copy -start_number 0 -hls_time 10 -hls_list_size 0 -f hls index.m3u8

## Configure the hostname

### Edit the docker-compose.yaml file

Change the following lines in the yaml file:

``
      - SERVER_CORS_ALLOW_ORIGINS=<site base url> # site base url, example: https://foo.bar.com 
``

``
      - "traefik.http.routers.website.rule=Host(`<base dns name>`)" # replace with base dns name, example: foo.bar.com
``

## Edit the traefik/traefik.yml file

Add the e-mail for Let's Encrypt certificate in the traefik configuration by changing the line below:

``
      email: <e-mail address for certificate> # example: foo@bar.com
``

## Create an empty acme.json file in the traefik folder

This empty acme.json file will be used to store certificate information.


# Open ports 443 and 80

These ports are required to be opened to obtain the Let's Encrypt certificate and for normal operation.

# Start the docker

On the first run, after starting the docker it will take a bit to for the certificate to be issued, so the site will not be immediately available.