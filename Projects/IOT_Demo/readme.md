# GCP_IOT_Demo
This is just a copy of code from GCP IOT Demo (https://github.com/GoogleCloudPlatform/python-docs-samples/tree/master/iot/api-client/end_to_end_example) with some minor tweaks:
Usage:

```
python "cloudiot_pubsub_example_mqtt_device.py" --project_id "<Project_id>" --registry_id "<GCTP IOT Core Registry ID>" --device_id "<GCP IOT Core Device ID>" --private_key_file "<Path (rel/abs) where rsa_private.pem is located>"  --algorithm "RS256" --cloud_region "<Region>" --ca_certs "<Path (rel/abs) where roots.pem is located>" --num_messages 10 --mqtt_bridge_hostname "mqtt.googleapis.com" --mqtt_bridge_port 8883 --jwt_timeout_minutes <JWT timeout minutes (60 default)> --message_type "event"
```
  
For Generating public/Private key use the following command (user Name not used in IOT Core):
  
```
  openssl req -x509 -newkey rsa:2048 -keyout rsa_private.pem -nodes \
    -out rsa_cert.pem -subj "/CN=unused"
```
  
 References:
 * [IOT Core Quick start](https://cloud.google.com/iot/docs/quickstart)
 * [GCP github Examples](https://github.com/GoogleCloudPlatform/python-docs-samples/tree/master/iot/api-client)
  
 ToDo:
  Include example Dataflow job to process the data and land into GCS bucket.