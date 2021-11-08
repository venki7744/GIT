Simplified steps:
* Open GCP Console.
* Connect to cloud shell
* Execute the following commands:

1. Define Variables:

```
export TEMPLATE_PATH="gs://venki-dataflow/templates/pubsubtogcsparquet.json"
export TEMPLATE_IMAGE="europe-west1-docker.pkg.dev/training-project-327506/venki-docker-repo/pubsubtogcsparquet-image:latest"
export REGION="europe-west1"
export PROJECT="training-project-327506"
export REPO="venki-docker-repo"
export TOPIC="venki_iot_topic"
export SUBSCRIPTION="venki_iot_topic_sub_dataflow_parquet"
export ROOTFOLDER="venki-dataflow"
export OUTFOLDER="venki-dataflow/streaming/output2"
```

2. Create artifact repo:

```
gcloud artifacts repositories create $REPO --repository-format=docker \
    --location=$REGION --description="docker-repo"
```

3. Copy contents in Dataflow folder to home:

4. Build docker image:

```
gcloud builds submit --tag $REGION-docker.pkg.dev/$PROJECT/$REPO/pubsubtogcsparquet-image
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
gcloud dataflow flex-template run "pubsubtogcsparquet-`date +%Y%m%d-%H%M%S`" \
    --template-file-gcs-location "$TEMPLATE_PATH" \
    --max-workers=1 \
    --region=europe-west1 \
    --staging-location="gs://$ROOTFOLDER/staging" \
    --temp-location="gs://$ROOTFOLDER/temp" \
    --worker-machine-type='n1-standard-2' \
    --region "$REGION" \
    --parameters input_subscription="projects/$PROJECT/subscriptions/$SUBSCRIPTION" \
    --parameters window_size=1 \
    --parameters output_path="gs://$OUTFOLDER" \
    --parameters num_shards=5
```

8. (Optional) Running with dataflow-prime enabled (need to enable Cloud Autoscale API):

```
gcloud dataflow flex-template run "pubsubtogcsparquet-`date +%Y%m%d-%H%M%S`" \
    --template-file-gcs-location "$TEMPLATE_PATH" \
    --max-workers=1 \
    --region=europe-west1 \
    --staging-location="gs://$ROOTFOLDER/staging" \
    --temp-location="gs://$ROOTFOLDER/temp" \
    --additional-experiments=enable_prime \
    --region "$REGION" \
    --parameters input_subscription="projects/$PROJECT/subscriptions/$SUBSCRIPTION" \
    --parameters window_size=1 \
    --parameters output_path="gs://$OUTFOLDER" \
    --parameters num_shards=5
```