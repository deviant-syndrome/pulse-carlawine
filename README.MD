# pulse-carlawine
#### Containerised Carla VST host with Win32/Win64 VST bridging
 
This container image will enable you to run a simple VST host ([Carla](https://kx.studio/Applications:Carla)), where you can load Win32/Win64 VST plugins, 
You can monitor the audio output on your host machine default audio device through Pulseaudio tcp sink. 

Audio recording within the container can be achieved with some additional tools installed.

This Pulseaudio-oriented setup was the simplest way to get sound from the container on MacOS for me.   

### Acknowledgements 
The dockerfile is based on the similar works of [Jess Frazelle](https://github.com/jessfraz/dockerfiles/tree/master/pulseaudio) for PulseAudio
 support and [Scott Hardy](https://github.com/scottyhardy/docker-wine) for X session support.
 
## Setup:

#### Prerequisites:
* Docker
* XQuartz
* Pulseaudio (available on Homebrew)

Step 1. Build the image
```bash
docker build pulse-carlawine -t deviant-syndrome/pulse-carlawine:latest
```
Step 2. Setup X forwarding

To get the X forwarding from container, which would act as a thin client of sorts. We will be using XQuartz for MacOS. From the XQuartz
 preferences, in the security tab, make sure Allow connections from network clients is enabled. Restart XQuartz. Then:
```bash
xhost +localhost
```

## Usage

Run pulseaudio sink on host machine:
```bash
pulseaudio --load=module-native-protocol-tcp --exit-idle-time=-1 --daemon
```
Run docker container:
```bash
docker run -it -e DISPLAY=host.docker.internal:0 -e PULSE_SERVER=docker.for.mac.localhost -v ~/.config/pulse:/home/pulseaudio/.config/pulse --entrypoint /bin/bash --ipc=host --rm deviant-syndrome/pulse-carlawine:latest
```

Copy your DLLs to container's filesystem. Alternatively you can download a Windows-based VST plugin installed and run it in container under Wine.
Then run Carla from the container. 
```bash
carla
```
Go to carla's prefrences and enable experimental features. After that you will be able to drag and drop your VST DLL to Carla's rack. 





