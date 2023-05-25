# In this section of ci/cd , we have the Dockerfile to build jenkins image, and we need to build it using the tag myjenkins

build image -t myjenkins .

# and the reason for using this tag is because we have a yaml deployment manifest to start jenkins which the image name is already myjenkins

