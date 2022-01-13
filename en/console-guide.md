## Compute > Image Builder > Console User Guide
### Create Image Template
Create an image template. The image builder creates an image based on the contents of the image template you created in advance.

<table class="it" style="padding-top: 15px; padding-bottom: 10px;">
  <tr>
    <th>Item</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Image Template Name </td>
    <td>Up to 255 English characters or 85 Korean characters</td>
  </tr>
  <tr>
    <td>OS</td>
    <td>OS type, distribution, and version of the private image to create</td>
  </tr>
  <tr>
    <td>Application</td>
    <td>Application installation components<br/>Multiple applications can be selected, but the selection may be limited if there is a conflict between the applications.</td>
  </tr>
  <tr>
    <td>Minimum Block Storage (GB)</td>
    <td>Minimum block storage size required to create an instance using an image</td>
  </tr>
  <tr>
    <td>User Script </td>
    <td>Script to be executed after application installation</td>
  </tr>
  <tr>
    <td>Description</td>
    <td>Image template description </td>
  </tr>
</table>

> [Note]
> As of January 2022, only Linux OS and user scripts in shell script format are supported.

<br/>

> [Note]
> User scripts run with the default user account. If you have a command that requires a root privilege, you must configure the script to run the command to gain the privilege first.
> The first line of the user script must start with a shebang. In general, `#!/bin/bash -e` is used.
> An error in the user script can cause the image build to fail. To determine the cause of the error, see the detailed log in the **Build Details** window.

### Update Image Template
You can modify the contents of an image template by selecting an image template and clicking the **Update Image Template** button.

### Delete Image Template
You can delete an image template by selecting an image template and clicking the **Delete Image Template** button.

### Build Image
Select an image template and click the **Build Image** button to start the build.

<table class="it" style="padding-top: 15px; padding-bottom: 10px;">
  <tr>
    <th>Item</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Image Name</td>
    <td>Up to 255 English characters</td>
  </tr>
  <tr>
    <td>Base Image</td>
    <td>The OS image used as the base when creating a private image<br/>The list of base images you can choose from depends on the applications stored in the image template.</td>
  </tr>
</table>

### Image Template Details
If you select an image template, you can check the information of the image template in the bottom tab.

### Image Template Build Status
Check the list of build tasks for the selected image template. You can select a build task to view details or cancel a build task in progress.

* **Build Details**: View details of an ongoing or finished build task. If the build failed, you can check the cause of the failure through the detailed log.
* **Cancel Build**: Cancel a build task in progress. If the build task does not end due to an error in a user script, or if the build needs to be restarted for other reasons, you can use Cancel Build to end the task and clean up the resources used in the build.

### Image List
You can see a list of images created with the selected image template.
