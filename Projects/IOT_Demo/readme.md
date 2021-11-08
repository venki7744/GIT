# GCP_IOT_Demo
This is just a copy of code from GCP IOT Demo (https://github.com/GoogleCloudPlatform/python-docs-samples/tree/master/iot/api-client/end_to_end_example) with some minor tweaks:

Quick and simple steps:

In a cloud shell, perform the following:

1. Declare few variables:

```
  export TOPIC="venki_iot_topic"
  export REGISTRY="venki_iot_20211028"
  export PROJECT="training-project-327506"
  export REGION="europe-west1"
  export DEVICE="venki_iot_device_20211028"
```

2. Create a Pub/Sub topic for receving IOT messages:

```
  gcloud pubsub topics create $TOPIC
```

3. Generate public/Private keys:

```
  openssl req -x509 -newkey rsa:2048 -keyout rsa_private.pem -nodes \
    -out rsa_cert.pem -subj "/CN=unused"
```

4. Create a Device registry:

```
  gcloud iot registries create $REGISTRY \
      --project=$PROJECT \
      --region=$REGION \
      --enable-mqtt-config \
      --enable-http-config \
      --event-notification-config=topic=projects/$PROJECT/topics/$TOPIC
```

5. Add device to Registry:

```
  gcloud iot devices create $DEVICE \
    --project=$PROJECT \
    --region=$REGION \
    --registry=$REGISTRY \
    --public-key path=rsa_cert.pem,type=rsa-x509-pem
```

6. Download CA certificate from [CA Certificate](https://pki.goog/roots.pem) to certificates folder (local).

7. Download private key generated (rsa_private.pem) to the certicates folder (local).

8. Run the following locally:

```
python "cloudiot_pubsub_example_mqtt_device.py" --project_id "<Project_id>" --registry_id "<GCTP IOT Core Registry ID>" --device_id "<GCP IOT Core Device ID>" --private_key_file "<Path (rel/abs) where rsa_private.pem is located>"  --algorithm "RS256" --cloud_region "<Region>" --ca_certs "<Path (rel/abs) where roots.pem is located>" --num_messages <Number of messages> --mqtt_bridge_hostname "mqtt.googleapis.com" --mqtt_bridge_port 8883 --jwt_timeout_minutes <JWT timeout minutes (60 default)> --message_type "event"
```
  
 References:
 * [IOT Core Quick start](https://cloud.google.com/iot/docs/quickstart)
 * [GCP github Examples](https://github.com/GoogleCloudPlatform/python-docs-samples/tree/master/iot/api-client)
  
 The repo also contains Dataflow flex-template to process streaming data.
 Reference:
 * [Dataflow Flex Template](https://cloud.google.com/dataflow/docs/guides/templates/using-flex-templates)
 * [Quick Start Docker Build](https://cloud.google.com/build/docs/quickstart-build#build_using_dockerfile)