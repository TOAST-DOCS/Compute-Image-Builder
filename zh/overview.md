## Compute > Image Builder > Overview

Image Builder is a service that creates a private image that meets the user's requirements based on the OS image provided by NHN Cloud.

## Service Features
* You can easily create a private image by combining the OS image provided by NHN Cloud, application installation components, and user scripts.
* By automating the process of creating images from instances, you can minimize errors that can occur during the work process.
* Because the service uses OS images with basic security configuration, you can create a private image that is safe from security threats.
* A variety of continuously managed application installation components are available.

> [Note]
> The Image Builder service is only available in Korea (Pangyo) and Korea (Pyeongchon) regions as of October 2021.

## Image Template
An image template is a document that includes information for creating an image. You can keep your private image up to date by writing application installation components and user scripts and only changing the OS image that is updated periodically.

## Build Task
Build tasks are managed per image template. You can check the detailed log of in-progress or completed build tasks, and check the list of created private images.

## Private Image
Private images created using Image Builder can be managed in the Image service (**Compute > Image**). For more information, refer to [Image Service User Guide](/Compute/Image/en/overview/).
