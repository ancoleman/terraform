## 2 x VM-Series / 2 x Spoke VPCs via Advanced Peering
This Terraform build creates two VM-Series in a transitive VPC.  Two spoke VPCs are created and are connected to the Transit VPC via GCPs Advanced Peering. The build bootstraps the VM-Series from a GCP Storage Bucket (created during deployment).  After the Terraform build completes, several manual steps must be performed to enable transitive routing.  The manual changes are required since they cannot be performed through Terraform, yet.

### Overview
* 5 x VPCs (mgmt, untrust, trust, spoke1, & spoke2) with relevant peering connections
* 2 x VM-Series (BYOL / Bundle1 / Bundle2)
    *  Secures internet-to-spokes, spoke-to-internet, spoke-to-spoke 
* 2 x Ubuntu VM in spoke1 VPC (install Apache during creation)
* 1 x Ubuntu VM in spoke2 VPC
* 1 x GCP Public Load Balancer (VM-Series as backend)
* 1 x GCP Internal Load Balancer (spoke1 VM's as backend)
* 1 x GCP Storage Bucket for VM-Series bootstrapping (random string appended to bucket name for global uniqueness)

### Diagram
<p align="center">
<img src="https://raw.githubusercontent.com/wwce/terraform/master/gcp/adv_peering_2fw_2spoke/images/diagram.png" width="500">
</p>


### Prerequistes 
1. Working installation of Terraform
2. Access to GCP Console

After deployment, the firewalls' username and password are:
     * **Username:** paloalto
     * **Password:** Pal0Alt0@123

### Deployment
1.  Download the **adv_peering_2fw_2spoke** directory.
2.  In an editor, open `variables.tf` and set values for the following variables
| Variable        | Description |
| ------------- |-------------|
| `main_project` | Project ID for the VM-Series, VM-Series VPCs, GCP storage bucket, & public load balancer. |
| `main_project_auth_file` | Authentication key file for main_project |
| `spoke1_project` | Project ID for spoke1 VMs, VPC, & internal load balancer |
| `spoke1_project_auth_file`| Authentication key file for spoke1_project |
| `spoke2_project` | Project ID for spoke2 VM & VPC |
| `spoke2_project_auth_file` | Authentication key file for spoke2_project |
| `ubuntu_ssh_key` | Public key used to authenticate to Ubuntu VMs (**user must be ubuntu**) |
| `vmseries_image` | Uncomment the VM-Series license you want to deploy |

3.  Download project authenication key files to the main directory of the terraform build.
<p align="center">
<img src="https://raw.githubusercontent.com/wwce/terraform/master/gcp/adv_peering_2fw_2spoke/images/directory.png">
</p>

4. Execute Terraform
```
adv_peering_2fw_2spoke $ terraform init
adv_peering_2fw_2spoke $ terraform plan
adv_peering_2fw_2spoke $ terraform apply
```

5. After deployment finishes, for EACH PEER, enable **Import custom routes** & **Export custom routes** 

<p align="center">
<img src="https://raw.githubusercontent.com/wwce/terraform/master/gcp/adv_peering_2fw_2spoke/images/peering.png">
</p>

6. Remove default GCP VPC route for spoke1-vpc, spoke2-vpc, & trust-vpc

<p align="center">
<img src="https://raw.githubusercontent.com/wwce/terraform/master/gcp/adv_peering_2fw_2spoke/images/routes.png">
</p>

7. From Terraform output, go to `GLB-ADDRESS = http://35.244.207.26` in a web browser.  NOTE: IT MAY TAKE SEVERAL MINUTES FOR SPOKE1 VMs TO FULLY INSTALL APACHE SETUP.
<p align="center">
<img src="https://raw.githubusercontent.com/wwce/terraform/master/gcp/adv_peering_2fw_2spoke/images/web.png">
</p>

## Support Policy
The guide in this directory and accompanied files are released under an as-is, best effort, support policy. These scripts should be seen as community supported and Palo Alto Networks will contribute our expertise as and when possible. We do not provide technical support or help in using or troubleshooting the components of the project through our normal support options such as Palo Alto Networks support teams, or ASC (Authorized Support Centers) partners and backline support options. The underlying product used (the VM-Series firewall) by the scripts or templates are still supported, but the support is only for the product functionality and not for help in deploying or using the template or script itself.
Unless explicitly tagged, all projects or work posted in our GitHub repository (at https://github.com/PaloAltoNetworks) or sites other than our official Downloads page on https://support.paloaltonetworks.com are provided under the best effort policy.