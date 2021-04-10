# oci-api-lab
Explore basic OCI-CLI commands by provisioning, altering, querying and deprovisioning a Compute Instance on OCI.


## Interacting with OCI APIs

- [SDKs](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/sdks.htm)
- [OCI-CLI](https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/cliinstall.htm#Quickstart)
	
<default>
	<summary>You can also set up OCI-CLI by following these steps</summary>

1. Create the directory whose name and location is reserved for OCI-CLI.
<pre>
mkdir ~/.oci
</pre>
2. Create an API signing key pair in ~/.oci (i.e. 1 x public, 1 x private).
<pre>
oci setup keys
</pre>
3. Register the public API signing key to your User page in OCI

- Navigate to cloud.oracle.com
- Sign into OCI with your Tenancy credentials
- Click the icon that resembles a person on the right-hand side of the top of the screen.
- Click your name under "Profile"
- Click <b>API Keys</b>
- Click <b>Add API Key</b>
- Click <b>Paste Public Key</b>
- Paste your public key, <b>~/.oci/oci_api_key_public.pem</b> into the field
- Click <b>Add</b>
- Copy the contents of <b>Configuration File Preview</b> to your clipboard, and paste them into a file on your machine called ~/.oci/config. The name and location of this file is reserved for OCI. Be sure to update the value that gets assigned to key_file with the path to your private key file (by default, <b>~/.oci/oci_api_key.pem</b>)

4. Set the appropriate permissions on certain files
<pre>
chmod 600 ~/.oci/oci_api_key.pem
chmod 600 ~/.oci/config
</pre>

5. See if you can query for and return your Object Storage namespace, which should be equivalent to the name of your tenancy. Note that your OCI user would need sufficient OCI IAM privileges to return this information.
<pre>
oci os ns get
</pre>
<details>
	<summary>The output</summary>
<pre>
{
  "data": "YOUR_OBJECT_STORAGE_NAMESPACE"
}
</pre>
</details>

6. See if you can list all of the users in your tenancy. Again, note that your OCI user would need sufficient OCI IAM privileges to return this information.
oci iam user list

<details>
	<summary>Compute Instance Lab</summary>

### Overview
Explore basic OCI-CLI commands by provisioning a Compute Instance on OCI, stopping the Compute Instance, verifying its STOPPED status, and then deprovisioning it.

##### Prerequisites
- Access to a Tenancy
- OCI-CLI set up on your local machine (see instructions above)
- Sufficient OCI IAM privileges to READ regions, availability domains, to USE Subnets and Compartments, and to MANAGE Compute Instances as well as Buckets and Objects in Object Storage
- Availability of a shape of a compute that you wish to deploy. You can check the availability of resources by navigating:
<pre>
cloud.oracle.com > Hamburger Menu > Governance > Limits, Quotas and Usage
</pre>

##### Step 1. Find a Region
List all regions available to the Tenancy. You will see the keys and the names (a.k.a. identifiers) of the regions. Identify the value of the <b>"name"</b> key of the region where you will deploy the Compute Instance.
oci iam region list

##### Step 2. Find an Availability Domain
List all of the availability domains in your region of interest, and identify the availability domain where you will deploy the Compute Instance.
- Replace <b>REGION_IDENTIFIER</b> with the value from the output of the previous command.
<pre>
oci iam availability-domain list --region REGION_IDENTIFIER
</pre>

##### Step 3. Find a Compartment
List all of the compartments in your tenancy, and identify the OCID of the Compartment where you will deploy the Compute Instance. Identify the value of the <b>"id"</b> key of the Compartment where you will deploy the Compute Instance.
<pre>
oci iam compartment list --compartment-id-in-subtree true
</pre>

##### Step 4. Find a Fault Domain
List all of the fault domains in your availability domain of interest. Identify the value of the <b>"name"</b> key of the Fault Domain where you will deploy the Compute Instance.
<pre>
oci iam fault-domain list --region REGION_IDENTIFIER --availability-domain AD_NAME --compartment-id COMPARTMENT_OCID
</pre>

##### Do you have an SSH key pair?
<details>
<summary>Yes</summary>
You can proceed to the next step.
</details>

<details>
<summary>No</summary>

##### Step 5. Create an SSH key pair
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

##### Step 6. Provision the Compute Instance
Assemble the information you've gathered to specify deployment details of a Compute Instance, and execute the command to create (provision) the Compute Instance. Some parameters are filled in with sample values. If you wish to deploy a shape that is not a <b>Flex shape</b>, be sure to omit the <b>--shape-config</b> flag and parameter from this command. Identify the value of the <b>"id"</b> key of the Compute Instance, once the information about the Compute Instance is returned upon deployment.
- Replace <b>SSH_PUBLIC_KEY</b> with the contents of the public SSH key file that you created in the previous command.
- Replace <b>IMAGE_OCID</b> with an image OCID that you can find on [this page](https://docs.oracle.com/en-us/iaas/images/). Find the image you wish to use, and be sure to use the OCID that corresponds to the region identifier you are using.
<pre>
oci compute instance launch --region REGION_IDENTIFIER --availability-domain AD_NAME --fault-domain FD_NAME --shape VM.Standard.E3.Flex --subnet-id SUBNET_OCID --compartment-id COMPARTMENT_OCID --boot-volume-size-in-gbs 100 --image-id IMAGE_OCID --shape-config '{"ocpus": 24.0}' --display-name test-instance --hostname-label test-instance --metadata '{"ssh_authorized_keys": "SSH_PUBLIC_KEY"}'
</pre>

##### Step 7. Stop the Compute Instance
Perform a <b>stop</b> action on the Compute Instance. You can read about the actions you can perform on a Compute Instance [here](https://docs.oracle.com/en-us/iaas/tools/oci-cli/2.22.2/oci_cli_docs/cmdref/compute/instance/action.html).
<pre>
oci compute --region REGION_IDENTIFIER instance action --instance-id COMPUTE_INSTANCE_OCID --action stop
</pre>

##### Step 8. Verify the STOPPED state
Get information about the Compute Instance, and observe that it is in the <b>STOPPED</b> state, since you have stopped the instance using the previous command.
<pre>
oci compute --region REGION_IDENTIFIER instance get --instance-id COMPUTE_INSTANCE_OCID
</pre>

##### Step 9. Deprovision the Compute Instance
Execute the command to terminate (deprovision) the Compute Instance.
<pre>
oci compute --region REGION_IDENTIFIER instance terminate --instance-id COMPUTE_INSTANCE_OCID
</pre>
</details>
</details>
