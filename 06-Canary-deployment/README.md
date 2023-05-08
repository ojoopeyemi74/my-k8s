# in this section we talk about CANAry deployment, upgrading to a new version without a down time

- steps: we have been able to add a new deployment with the new version and add a new lable in the pod template section

- next: we have also been able to update the selector in sevice to have the new label 

- next: applying -f on the files will create a new version of the image, and now we can decide to remove the old image deployment at our which 