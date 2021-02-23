# bbb-streaming

Use this app to live stream your BigBlueButton classes to Youtube or Facebook.

When you want hundreds, or even thousands of users, to join your BigBlueButton classes, you may not be able to use regular BigBlueButton setup, as that is limited to around 100 users per session. Then live streaming to your users is a powerful solution.   

### How it works?

All you need is Docker running on your machine and a media server such as Youtube or Facebook to stream to.

Once you fetch bbb-streaming on your BigBlueButton server, you need to update a few environment variables including access to your BigBlueButton server, ID of the BigBlueButton meeting/class that you want to stream and Youtube or Facebook RTMP url (see below).

After changing environment variables, start the docker container and start your BigBlueButton class. 

You will notice that the live streaming bot joins your BigBlueButto class with the name `Live`. 

On your Youtube or Facebook live streaming page (see below), you would notice your class getting streamed into. 

Share the streaming URL with your users to view the live streaming of your class. 

### What to stream: video, presentation or both
You can live stream in the following ways:
* Only video: set `BBB_HIDE_PRESENTATION` to `true` in `docker-compose.xml`.
* Only presentation: set `BBB_HIDE_PRESENTATION` to `false` in `docker-compose.xml` and don't turn on the video for the presenter. 
* Both video and presentation: set `BBB_HIDE_PRESENTATION` to `false` in `docker-compose.xml`. The presenter's video will appear above the presentation, as in the default BigBlueButton layout.

## Install


### 1. Installing Docker
```ssh
apt install apt-transport-https ca-certificates curl software-properties-common
apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
add-apt-repository “deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable”
apt update
apt install docker-ce
```

### 2. Installing Docker Compose
```ssh
curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

### 3. Fetching bbb-streaming
```sh
wget -O docker-compose.yml https://raw.githubusercontent.com/manishkatyan/bbb-streaming/master/examples/docker-compose.yml.example
# change configuration
docker-compose up -d
docker-compose down
``` 

### 4. Set Environment Variable

You need to set some environment variables to run the container.

#### Required settings
* BBB_URL - URL to BBB including http/https e.g. https://your_BigBlueButton_server/bigbluebutton/api
* BBB_MEETING_ID - ID of the BBB Meeting (You can get the ID via an API call: https://your_bbb_server/bigbluebutton/api/getMeetings?checksum=<checksum>)
* BBB_SECRET - Secret of your BBB installation (You can get the secret with: bbb-conf --secret)
* BBB_STREAM_URL - URL of your streaming server including rtmp. (Example Youtube: rtmp://a.rtmp.youtube.com/live2/wf24-fttr-0228-uvas-d5hh, Facebook: rtmps://live-api-s.facebook.com:443/rtmp/3723139434365952?s_bl=1&s_ps=1&s_psm=1&s_sw=0&s_vt=api-s&a=Abzo1Ejb-3Wh_7qt)
* BBB_VIEWER_STREAM_URL - URL for your users to view live streaming. This would be displayed in public chat.

#### Optional settings
* BBB_HIDE_PRESENTATION - Set as true to hide presentation and show only video during live streaming
* BBB_START_MEETING - For bot to start the session as a moderator
* BBB_ATTENDEE_PASSWORD - attendee password (optional - the attendee password of moodle/greenlight/API-MATE for the bot to join the session )
* BBB_MODERATOR_PASSWORD - moderator password (optional - the moderator password of moodle/greenlight/API-MATE for the bot to create the session as a moderator)
* BBB_MEETING_TITLE - meeting title (optional - requires only if the bot creates the meeting as a moderator)
* BBB_DOWNLOAD_MEETING= - download / save BigBlueButton meeting in lossless mkv format
* BBB_INTRO= - play intro file (can be a local file in videodata folder e.g. /video/intro.mp4 or a url of a mediastream e.g. https://my.intro.stream)
* BBB_BEGIN_INTRO_AT=04:40 - begin the intro at position (optional, e.g. 00:00:05)
* BBB_END_INTRO_AT= - end intro after (optional, e.g. 01:00:00 - after one hour)
* BBB_USER_NAME - the username to join the meeting. (Default: Live)
* BBB_SHOW_CHAT - shows the chat on the left side of the window (Default: false)
* TZ - Timezone (Default: Europe/Vienna)
* BBB_CUSTOM_CSS - Custom CSS to change, for example size of video, using url encoded CSS. URI encode your css here: https://www.urlencoder.org/

## Streaming with API-MATE
```sh
# Get API-MATE URL
bbb-conf --secret
# Ensure meetingId and attendeePW is the same in API-MATE and docker-compose.yml
# Restart docker
```
You can use API-MATE to quickly test streaming functionality.

Get API-MATE url as shown above and open it in a Google Chrome browser. Ensure that meetingID and attendee Password in API-MATE is the same as in docker-compose.yml. 

First click on create URL and then join as a moderator. 

You will get into BigBlueButton session as a moderator. 

Go to 'bbb-streaming' directory and start the docker, if it's not already running.

You would notice a new user `Live` has joined your BigBlueButton session.  

## Streaming on Youtube
```sh
# Edit docker-compose.yml to update RTMP url for Youtube
BBB_STREAM_URL=rtmp://a.rtmp.youtube.com/live2/wf28-fttr-0228-uvas-d5hh
docker-compose down
docker-compose up -d
```
Go to [Youtube Studio](https://www.youtube.com/live_dashboard_splash?nv=1)
Copy Stream URL and Stream key and combine them, as shown above, to create `BBB_STREAM_URL` in docker-compose.yml.

Restart docker container. 

## Streaming on Facebook
```sh
# Edit docker-compose.yml to update RTMP url for Facebook
BBB_STREAM_URL=rtmps://live-api-s.facebook.com:443/rtmp/3723139434365952?s_bl=1&s_ps=1&s_psm=1&s_sw=0&s_vt=api-s&a=Abzo1Ejb-3Gh_7et
docker-compose down
docker-compose up -d
```
Go to the Facebook page where you want to live stream and click on `Create > Live`. Select `Use Stream Key` as the method that you want to live strem with. Copy `Server URL` and `Stream Key`, joining them together, to form the value of `BBB_STREAM_URL` in docker-compose.xml.

Restart the docker container.

Use API-MATE (see above) to test live streaming on Facebook.

On your Facebook live streaming page, you would notice the video of your BigBlueButton class is being shown. Click on `Go Live` to start live streaming. 

You have several ways to share live streaming url with your users including:
- Show Live Tab on Page: Example - https://www.facebook.com/higheredlab/live/ 
- Live Video URL: Example - https://www.facebook.com/higheredlab/videos/408835117173971/
- Embed Live Video: You can embed live video into any page on your website

Your users can use Facebook chat, shown along side live streaming video, to interact during the live streaming. 

## Known Limitations
* the streamer does not reconnect, if the connection to BigBlueButton gets lost
* when using breakoutrooms, the streamer will show the popup of the invitation and not be able to get back to the conference

## More on BigBlueButton

Check-out the following apps to further extend features of BBB.

### [bbb-mp4](https://github.com/manishkatyan/bbb-mp4)
With this app, you can convert a BigBlueButton recording into MP4 video and upload to S3. You can convert multiple MP4 videos in parallel or automate the conversion process.

### [bbb-twilio](https://github.com/manishkatyan/bbb-twilio)

Integrate Twilio into BigBlueButton so that users can join a meeting with a dial-in number. You can get local numbers for almost all the countries.

### [bbb-optimize](https://github.com/manishkatyan/bbb-customize)

Better audio quality, increase recording processing speed, dynamic video profile, pagination, fix 1007/1020 errors and use apply-config.sh to manage your customizations are some key techniques for you to optimize and smoothly run your BigBlueButton servers.

### [100 Most Googled Questions on BigBlueButton](https://higheredlab.com/bigbluebutton-guide/)

Everything you need to know about BigBlueButton including pricing, comparison with Zoom, Moodle integrations, scaling, and dozens of troubleshooting.

#### Inspired by

bbb-streaming app builds on the ideas from several other projects, including:

[BigBlueButton-liveStreaming](https://github.com/aau-zid/BigBlueButton-liveStreaming)
