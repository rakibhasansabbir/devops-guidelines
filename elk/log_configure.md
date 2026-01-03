# ELK Log Configuration – Filebeat Instructions

## 1. Add Filebeat Docker Container
- Image: `docker.elastic.co/beats/filebeat`
- Purpose: collect application logs and send them to Logstash

## 2. Update `docker-compose.yml`
- Add volume mount for `filebeat.yml`
- Add volume mount for application log directory from host
- Without volume mount, Filebeat cannot read logs

## 3. Update `filebeat.yml`
### filebeat.inputs
- Define log file paths
- Paths must exist inside Filebeat container
- Use application log directory

### output.logstash
- Configure Logstash host and port
- Filebeat sends logs to Logstash (not Elasticsearch)

## 4. Problem
- Application logs exist only inside application container
- Filebeat cannot read container-internal logs

## 5. Solution
- Update `deploy-docker.sh`
- Modify `docker run` command to mount logs to host

Example:
`-v "$DEPLOY_PATH/logs":/var/www/app/storage/logs`

## 6. Log Flow
Application
→ container logs
→ host-mounted volume
→ Filebeat-mounted volume
→ Filebeat
→ Logstash
→ Elasticsearch
→ Kibana

## 7. Mandatory Checks
- Logs exist on host
- Logs path mounted into Filebeat
- `filebeat.inputs.paths` is correct
- `output.logstash` host and port are correct
- Logstash beats input is enabled
