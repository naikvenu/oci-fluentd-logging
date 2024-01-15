# Instructions to build your OCI Fluentd Logging Image with all the necessary packages

# You can either use your workstation or OCI console to perform these steps:

# Firstly, create a OCI container registry

Developer Services -> Containers & Artifacts -> Container Registry
Create a new Private repository

# Provide Necessary IAM permissions for the user to push into this repository

Login to your container registry, Do note you would require your Auth Token as password.
  
docker login syd.ocir.io

# Build your image

docker build -f Dockerfile -t syd.ocir.io/<tenancy>/<ocir-repo-name>/custom-logging-fluentd:latest .

# Push your image
docker push syd.ocir.io/<tenancy>/<ocir-repo-name>/custom-logging-fluentd:latest


