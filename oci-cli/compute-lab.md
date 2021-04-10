# Compute Instance Lab: OCI-CLI Basics

### Overview
Explore basic OCI-CLI commands by provisioning a Compute Instance on OCI, stopping the Compute Instance, verifying its STOPPED status, and then deprovisioning it.

### Prerequisites
- Access to a Tenancy
- OCI-CLI set up on your local machine (see instructions above)
- Sufficient OCI IAM privileges to READ regions, availability domains, to USE Subnets and Compartments, and to MANAGE Compute Instances as well as Buckets and Objects in Object Storage
- Availability of a shape of a compute that you wish to deploy. You can check the availability of resources by navigating:
<pre>
cloud.oracle.com > Hamburger Menu > Governance > Limits, Quotas and Usage
</pre>

### Step 1. Find a Region
List all regions available to the Tenancy. You will see the keys and the names (a.k.a. identifiers) of the regions. Identify the value of the <b>"name"</b> key of the region where you will deploy the Compute Instance.
oci iam region list

### Step 2. Find an Availability Domain
List all of the availability domains in your region of interest, and identify the availability domain where you will deploy the Compute Instance.
- Replace <b>REGION_IDENTIFIER</b> with the value from the output of the previous command.
<pre>
oci iam availability-domain list --region REGION_IDENTIFIER
</pre>

### Step 3. Find a Compartment
List all of the compartments in your tenancy, and identify the OCID of the Compartment where you will deploy the Compute Instance. Identify the value of the <b>"id"</b> key of the Compartment where you will deploy the Compute Instance.
<pre>
oci iam compartment list --compartment-id-in-subtree true
</pre>

### Step 4. Find a Fault Domain
List all of the fault domains in your availability domain of interest. Identify the value of the <b>"name"</b> key of the Fault Domain where you will deploy the Compute Instance.
<pre>
oci iam fault-domain list --region REGION_IDENTIFIER --availability-domain AD_NAME --compartment-id COMPARTMENT_OCID
</pre>

### Do you have an SSH key pair?
<details>
<summary>Yes</summary>
You can proceed to the next step.
</details>

<details>
<summary>No</summary>

### Step 5. Create an SSH key pair
Create an SSH public key pair on your machine.
<details>
	<summary>Unix-based OS</summary>
If you are using a Unix-based OS, follow these instructions:

5.1. Create a directory called <b>~/.ssh</b> if it does not already exist. This is the default save location for an SSH key pair.
<pre>
mkdir -p ~/.ssh
</pre>

5.2. Create the SSH key pair.
<pre>
ssh-keygen
</pre>

5.3. Press Enter to choose the default options. With the default options, your SSH key pair will have no passphrase, consist of an SSH public key file named <b>id_rsa.pub</b>, and an SSH private key file named <b>id_rsa</b>. These files will exist in the <b>~/.ssh</b> directory.
</details>
<details>
	<summary>Windows</summary>
	If you are using Windows, please refer to these [instructions](https://docs.joyent.com/public-cloud/getting-started/ssh-keys/generating-an-ssh-key-manually/manually-generating-your-ssh-key-in-windows).
</details>
</details>

### Step 6. Provision the Compute Instance
Assemble the information you've gathered to specify deployment details of a Compute Instance, and execute the command to create (provision) the Compute Instance. Some parameters are filled in with sample values. If you wish to deploy a shape that is not a <b>Flex shape</b>, be sure to omit the <b>--shape-config</b> flag and parameter from this command. Identify the value of the <b>"id"</b> key of the Compute Instance, once the information about the Compute Instance is returned upon deployment.
- Replace <b>SSH_PUBLIC_KEY</b> with the contents of the public SSH key file that you created in the previous command.
- Replace <b>IMAGE_OCID</b> with an image OCID that you can find on [this page](https://docs.oracle.com/en-us/iaas/images/). Find the image you wish to use, and be sure to use the OCID that corresponds to the region identifier you are using.
<pre>
oci compute instance launch --region REGION_IDENTIFIER --availability-domain AD_NAME --fault-domain FD_NAME --shape VM.Standard.E3.Flex --subnet-id SUBNET_OCID --compartment-id COMPARTMENT_OCID --boot-volume-size-in-gbs 100 --image-id IMAGE_OCID --shape-config '{"ocpus": 24.0}' --display-name test-instance --hostname-label test-instance --metadata '{"ssh_authorized_keys": "SSH_PUBLIC_KEY"}'
</pre>

### Step 7. Stop the Compute Instance
Perform a <b>stop</b> action on the Compute Instance. You can read about the actions you can perform on a Compute Instance [here](https://docs.oracle.com/en-us/iaas/tools/oci-cli/2.22.2/oci_cli_docs/cmdref/compute/instance/action.html).
<pre>
oci compute --region REGION_IDENTIFIER instance action --instance-id COMPUTE_INSTANCE_OCID --action stop
</pre>

### Step 8. Verify the STOPPED state
Get information about the Compute Instance, and observe that it is in the <b>STOPPED</b> state, since you have stopped the instance using the previous command.
<pre>
oci compute --region REGION_IDENTIFIER instance get --instance-id COMPUTE_INSTANCE_OCID
</pre>

### Step 9. Deprovision the Compute Instance
Execute the command to terminate (deprovision) the Compute Instance.
<pre>
oci compute --region REGION_IDENTIFIER instance terminate --instance-id COMPUTE_INSTANCE_OCID
</pre>