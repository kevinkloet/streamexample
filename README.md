this is an example of how to stream video to a websocket when the source of the rtsp stream is an external website.

1) Install ffmpeg (See [How to install ffmpeg on Debian / Raspbian](http://superuser.com/questions/286675/how-to-install-ffmpeg-on-debian)). Using ffmpeg, we can capture the webcam video & audio and encode it into MPEG1/MP2.

2) Install Node.js and npm (See [Installing Node.js on Debian and Ubuntu based Linux distributions](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions) for newer versions). The Websocket relay is written in Node.js

3) Install http-server. We will use this to serve the static files (view-stream.html, jsmpeg.min.js), so that we can view the website with the video in our browser. Any other webserver would work as well (nginx, apache, etc.):
`sudo npm -g install http-server`

4) Install git and clone this repository (or just download it as ZIP and unpack)

5) Change into the streamexample/ directory
`cd streamexample/`

6) Install the Node.js Websocket Library:
`npm install ws`

7) Start the Websocket relay. Provide a password and a port for the incomming HTTP video stream and a Websocket port that we can connect to in the browser:
`node websocket-relay.js supersecret 8081 8082`

8) In a new terminal window (still in the `streamexample/` directory, start the `http-server` so we can serve the view-stream.html to the browser:
`http-server`

9) Open the streaming website in your browser. The `http-server` will tell you the ip (usually `192.168.[...]`) and port (usually `8080`) where it's running on:
`http://192.168.[...]:8080/view-stream.html`

10) In a third terminal window, start ffmpeg to stream data and send it to the Websocket relay. Provide the password and port (from step 7) in the destination URL:
```
ffmpeg \
	-f rtsp -i rtsp://184.72.239.149/vod/mp4:BigBuckBunny_115k.mov -vcodec libx264 -movflags frag_keyframe+empty_moov \
	-f mpegts -codec:v mpeg1video -b:v 1000k -bf 0 -muxdelay 0.001 http://localhost:8081/supersecret
```

You should now see a live video in your browser. 

example based on [jsmpeg by phoboslab](https://github.com/phoboslab/jsmpeg)