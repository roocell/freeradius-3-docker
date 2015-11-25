# freeradius-3-docker
docker files for roocell/freeradius-3
https://hub.docker.com/r/roocell/freeradius-3/

includes a Dockerfile and a shell script to fix permissions on a mounted volume (for OSX)


// execute a bash shell inside the container
// we need to do this to play around with the config files inside the container
```
docker exec -i -t myfreeradius bash
```

// this will allow us to pull the files out into the host file system
// then we can either add them into the container as a data volume
// or add them into our data volume container
// NOTE: can't use wildcards
// NOTE: also had issue with copying directory
// docker cp <containerId>:/file/path/within/container /host/path/target
```
mkdir freeradius/config
```

// exec into the container and create a tarball
```
docker exec -i -t myfreeradius bash
tar cvfz freeradius.tar.gz freeradius
exit
```
```
docker cp myfreeradius:/etc/freeradius.tar.gz ./freeradius/config/
tar zxvf freeradius/config/freeradius.tar.gz -C freeradius/config
mv freeradius/config/freeradius/* freeradius/config/
rm -r freeradius/config/freeradius
```
// stop and rerun the freeradius container with the volume mounted
// while we're at it - let's mount a freeradius logging directory
```
docker rm -f myfreeradius
docker run -d -p 18131:1813/udp -p 18121:1812/udp \
	-v $PWD/freeradius/config:/etc/freeradius \
	-v $PWD/freeradius/logs:/var/log/freeradius \
        -it --name myfreeradius freeradius 
```
