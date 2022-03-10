---
title: "Create Image From Customized Volume"
date: 2022-01-24T15:57:51-05:00
weight: 30
draft: true
---

Now, we go back to the Cloud Shell for creating the new custom image.

1. Stop the VSI

```
ibmcloud is instance-stop ${CUSTOM_IMG_VSI_NAME}
```

2. Submit the request to create the custom image

``` bash
# Store Volume ID in a environment variable
SOURCE_VOLUME_ID=$(ibmcloud is instance ${CUSTOM_IMG_VSI_NAME} --output JSON | jq -r .volume_attachments[0].volume.id)
# Submit request to create image from the volume
ibmcloud is image-create ${CUSTOM_IMG_NAME} --source-volume ${SOURCE_VOLUME_ID}
```

This step can take some time depending on the image size. You can check webpage(where to check? take screenshot) for the status of the image creation.

3. When the image is created, we can delete the VSI that is no longer needed

```
ibmcloud is instance-delete ${CUSTOM_IMG_VSI_NAME}
```

