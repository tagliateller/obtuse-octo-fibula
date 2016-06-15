https://github.com/Azure/azure-content/blob/master/articles/virtual-machines/xplat-cli-azure-manage-vm-asm-arm.md

# ansible playbooks

$ export ANSIBLE_HOST_KEY_CHECKING=False

# Resource-Template

azure group create -n origin-group -l "West Europe"

azure group template validate -f azuredeploy.json -e azuredeploy.parameters.json -g origin-group

azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g origin-group -n origin-deployment



