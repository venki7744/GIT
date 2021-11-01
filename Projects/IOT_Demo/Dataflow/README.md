Simplified steps:
* Open GCP Console.
* Connect to cloud shell
* Execute the following commands:

1. Define Variables:

```
export TEMPLATE_PATH="gs://<GCS Bucket>/templates/pubsubtogcs.json"
export TEMPLATE_IMAGE="<REGION>-docker.pkg.dev/<PROJECT ID>/<REPO>/pubsubtogcs-image:latest"
export REGION="<REGION>"
export PROJECT="<PROJECT ID>"
export REPO="<REPO>"
export TOPIC="<IOT TOPIC>"
export SUBSCRIPTION="<SUBSCRIPTION ON IOT TOPIC>"
export ROOTFOLDER="<GCS BUCKET>"
export OUTFOLDER="<OUTPUT BUCKET PATH>"
```

2. Create artifact repo:

```
gcloud artifacts repositories create $REPO --repository-format=docker \
    --location=$REGION --description="docker-repo"
```

3. Copy contents in Dataflow folder to home:

4. Build docker image:

```
gcloud builds submit --tag $REGION-docker.pkg.dev/$PROJECT/$REPO/pubsubtogcs-image
```

5. Create Subscription on Topic:

```
gcloud pubsub subscriptions create --topic $TOPIC $SUBSCRIPTION
```

6. Build Dataflow Flex Template:

```
gcloud dataflow flex-template build $TEMPLATE_PATH \
      --image "$TEMPLATE_IMAGE" \
      --sdk-language "PYTHON" \
      --metadata-file "metadata.json"
```

7. Run Dataflow Pipeline:

```
gcloud dataflow flex-template run "pubsubtogcs-`date +%Y%m%d-%H%M%S`" \
    --template-file-gcs-location "$TEMPLATE_PATH" \
    --max-workers=1 \
    --region=europe-west1 \
    --staging-location="gs://$ROOTFOLDER/staging" \
    --temp-location="gs://$ROOTFOLDER/temp" \
    --worker-machine-type='n1-standard-2' \
    --region "$REGION" \
    --parameters input_topic="projects/$PROJECT/topics/$TOPIC" \
    --parameters input_subscription="projects/$PROJECT/subscriptions/$SUBSCRIPTION" \
    --parameters window_size=1 \
    --parameters output_path="gs://$OUTFOLDER/file-" \
    --parameters num_shards=5
```
