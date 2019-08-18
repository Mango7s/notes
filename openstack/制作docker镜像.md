docker save img -o  file_path
docker load -i file_path
openstack image create --file FILE_PATH --disk-format raw --container-format docker --public NAME

