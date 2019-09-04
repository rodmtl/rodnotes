---
layout: post
title:  ".NET core app in containers"
date:   2018-07-04 04:46:36 -0400
categories: VS2017 azure dev docker
---
## Using win 7 using VM virtual box. 

A. Install docker tool box. Include all the options even kitematic

B. Test Docker .NET core sample run:

{% highlight cmd %}
docker run -it --rm -p 8000:80 --name aspnetcore_sample microsoft/dotnet-samples:aspnetapp
{% endhighlight %}


Create .NET core project and enable docker support (Linux)

1. Build app and be sure it works local in IIS
2. Execute the docker run command after build in Release mode in VS (this will create the docker image with the needed tags).

{% highlight cmd %}
docker run -it --rm -p 9000:80 --name rodNet1 corecontdemo1
{% endhighlight %}

To debug docker in win7, so far not able to do it.

Seems to be an issue in VStudio docker tools:

<https://github.com/Microsoft/DockerTools/issues/37>


## HOST IMAGE IN DOCKER HUB and CI/CD	(<https://rodnet1.azurewebsites.net/home>)
Upload image to dockerhub:
- create user and repo in docker hub
Run: (Identify your image id before.)

{% highlight cmd %}
PS C:\Users\riveroro> docker login --username=rodfujitsu
Password:
Login Succeeded
PS C:\Users\riveroro> docker tag  cd9238b88075 rodfujitsu/democore:firsttry
PS C:\Users\riveroro> docker push rodfujitsu/democore:firsttry
The push refers to repository [docker.io/rodfujitsu/democore]
0e853f3faebc: Pushed
ee00626f0b05: Pushed
dbf5b70fea24: Mounted from microsoft/aspnetcore-build
3c15c451c1fd: Mounted from microsoft/aspnetcore-build
950477f4c12c: Mounted from microsoft/aspnetcore-build
a0ee4fe852a3: Mounted from microsoft/aspnetcore-build
e259f4adeb8b: Mounted from microsoft/aspnetcore-build
3b820f84f2bd: Mounted from microsoft/aspnetcore-build
fbbeadfbd3ca: Mounted from microsoft/aspnetcore-build
0f6f641d80ca: Mounted from microsoft/aspnetcore-build
76a66da94657: Mounted from microsoft/aspnetcore-build
0f3a12fef684: Mounted from microsoft/aspnetcore-build
firsttry: digest: sha256:c540fcda1bca1317f149c441ec3d6310a12ecf6eb59c41d22458b2a072b63b87 size: 2850
PS C:\Users\riveroro>
{% endhighlight %}

In Azure: Create app service for containers, point it to the dockerhub image.

For CD in azure goto the app service and container settings.

----

## HOST IMAGE IN AZURE CONATINER REGISTRY and CI/CD (<https://rodnet2.azurewebsites.net>)
Automate process CI+CD Docker image to azure web app service for containers. 

<https://docs.microsoft.com/en-us/vsts/pipelines/apps/containers/build?view=vsts&tabs=web>


- Create a container registry in azure (will host the images)
- setup app build in vsts/tfs with CI. Check that your build works, and check that the CI works pushing from VS
- add steps with docker compose. check tags (use latest flag since is simplier to handle)
- once finished you can go ahead and configure deployment (CD) <https://docs.microsoft.com/en-us/vsts/pipelines/apps/cd/deploy-docker-webapp?view=vsts>


CD configured from VSTS create errors from docker.dotnet. It was not able to find the image!

I realized in the local build in VS the image is built as platform Linux, but the automated build on vsts create a windows image.

Setup the build setup to use the linux agent. Now CI/CD workflow is working.

----

### Pushing a local image to Azure container registry 

To test if your local image works, push manually your image to ACR.

{% highlight cmd %}
PS C:\windows\system32> docker login rodcontainerreg.azurecr.io
Username: rodContainerReg
Password: lalalala
Login Succeeded
PS C:\windows\system32> docker tag hello-world rodcontainerreg.azurecr.io/hello-world
PS C:\windows\system32> docker push rodcontainerreg.azurecr.io/hello-world
The push refers to repository [rodcontainerreg.azurecr.io/hello-world]
2b8cbd0846c5: Pushed
latest: digest: sha256:d5c74e6f8efc7bdf42a5e22bd764400692cf82360d86b8c587a7584b03f51520 size: 524
PS C:\windows\system32> docker pull rodcontainerreg.azurecr.io/hello-world
Using default tag: latest
latest: Pulling from hello-world
Digest: sha256:d5c74e6f8efc7bdf42a5e22bd764400692cf82360d86b8c587a7584b03f51520
Status: Image is up to date for rodcontainerreg.azurecr.io/hello-world:latest
{% endhighlight %}