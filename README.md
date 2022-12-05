hardev@nutanix.com
# December 2022
# Acknowledgements: https://github.com/vPackets/ANSIBLE-vSphere-VCSA-OVA-Deploy
# I customized the above playbooks for HPOCs - original README is included for reference and installtion of Ansible hints.
# These playbooks will deploy a vmWare VCSA 7 to a Nutanix HPOC, it assumes that 4 ESXi nodes are available.
# The following is performed:
# 1. Deploy VCSA - you will need a VCSA .ova file, to get one you can mount a VCSA .iso and then copy the .ova from the vcsa folder 
#    from the mount point.  This is the only file I needed, no separate manifest or disk images.
#    The VCSA is seployed to datastore vmContiner1 - which is as per the original West cluster setup, this datastore ust be present.
# 2. Perform customization of the VCSA:
#    a. Create Datacenter
#    b. Create Cluster
#    c. Turn on HA with Admission Control at 25%
#    d. Disable PDL and APD
#    e. Enable DRS fully automated
#    f. Add Advanced Option for das.ignoreInsufficientHbDatastore
#    *** THERE ARE ADDITIONAL CUSTOMIZATIONS YOU WILL NEED TO MAKE MANUALLY - SEE BELOW
# 3. Add 4 ESXi nodes to the vCenter Cluster NOTE: This is forced - so if the nodes are being managed by
#    another vCenter they will be snatched away.
#
# You can now add the vCenter to PE/PC - I found that for the West clusters if you use the same IP addresses (recommended) then PE/PC
# will re-connect themselves.
#
# Additional customizations to be carried out - these are as per vSphere Administration Guide for Acropolis
# a. Configure VM Overrides
# b. Configure Host Failure Response
# c. Select vmContainer1 as the heartbeat datastore (Use datastores only from the specified list)
#
# Timings
# 1. Deployment of the OVA can take 45+ minutes - depends on how close your Ansible workstation is to the HPOC datacenter! [First Playbook]
# 2. Once the OVA is deployed it will take another 40 or so minutes for the customizations [First Playbook]
# 3. Then the Nutanix specific customizations happen for 5 minutes or so [Second Playbook]
# 4. Then the 4 ESXi nodes are pulled into the VCSA for 5 minutes or so [Third Playbook]
#
# To run these playbooks you will need an Ansible enabled workstation with the community.vmware modules installed (See the orignal README).
# As a test target you can deploy an HPOC: ESXi 7 as the hypervisor, Foundation only with NO PC/vCenter.  Once you have it login to PE and define
# a new storage container to be mounted on all ESXi hosts - name it vmContainer1.  If you are re-deploying the VCSA for the West ESXi cluster then this storage container is already defined.
#
# To run:
# Edit answerfile.yml to reflect your HPOC then
# $ ansible-playbook deploy.yml