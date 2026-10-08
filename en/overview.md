<!-- machine_translated: true -->

<!-- pre-align:aligned sig=4fb95f840e9f -->

<a id="compute-image-builder-overview"></a>
## Compute > Image Builder > Overview { #compute-image-builder-overview }

Image Builder is a service that creates a private image that meets the user's requirements based on the OS image or private images provided by NHN Cloud{% if "gov" in build_flags %} (for public institutions){% endif %}.

<a id="service-features"></a>
## Service Features { #service-features }
* You can easily create a private image by combining the base image, application installation components, and user scripts.
* By automating the process of creating images from instances, you can minimize errors that can occur during the work process.
* Because the OS images provided by NHN Cloud {% if "gov" in build_flags %} (for public institutions){% endif %} have basic security configuration applied, you can create a private image that is safe from security threats.
* A variety of continuously managed application installation components are available.

{% if "gov" not in build_flags %}> [Note]
> The Image Builder service is only available in Korea (Pangyo) and Korea (Pyeongchon) regions as of September 2023.

{% endif %}<a id="image-template"></a>

## Image Template { #image-template }
An image template is a document that includes information for creating an image. You can keep your private image up to date by writing application installation components and user scripts and only changing the OS image that is updated periodically.

<a id="build-task"></a>
## Build Task { #build-task }
Build tasks are managed per image template. You can check the detailed log of in-progress or completed build tasks, and check the list of created private images.

<a id="private-image"></a>
## Private Image { #private-image }
Private images created using Image Builder can be managed in the Image service (**Compute > Image**). For more information, refer to [Image Service User Guide](/Compute/Image/en/overview/).