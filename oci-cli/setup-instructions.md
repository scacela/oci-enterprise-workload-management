# Setup Instructions for OCI-CLI

#### Step 1. Create the directory whose name and location is reserved for OCI-CLI.
<pre>
mkdir ~/.oci
</pre>

#### Step 2. Install OCI-CLI
<details>
	<summary>Linux or Oracle Linux 8</summary>
	<p></p>
	If you are using a Linux or Oracle Linux 8, install OCI-CLI with this command.
<pre>
bash -c "$(curl -L https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.sh)"
</pre>
</details>
<details>
	<summary>Oracle Linux 7</summary>
	<p></p>
	If you are using a Oracle Linux 7, install OCI-CLI with yum.
<pre>
sudo yum install python36-oci-cli
</pre>
</details>
<details>
	<summary>Mac OS</summary>
	<p></p>
	If you are using a MAC OS, install OCI-CLI with homebrew.
<pre>
brew update && brew install oci-cli
</pre>
</details>
<details>
	<summary>Windows</summary>
	<p></p>
	If you are using Windows, please refer to the Windows section in the Quickstart from the Oracle documentation.

[Quickstart - Oracle Docs](https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/cliinstall.htm#Quickstart)
</details>

#### Step 3. Create an API signing key pair
The following command will create an API signing key pair (i.e. 1 x public, 1 x private) in <b>\~/.oci</b>.
<pre>
oci setup keys
</pre>

#### Step 4. Register the public API signing key to your User page in OCI

- Navigate to cloud.oracle.com
- Sign into OCI with your Tenancy credentials
- Click the icon that resembles a person on the right-hand side of the top of the screen.
- Click your name under "Profile"
- Click <b>API Keys</b>
- Click <b>Add API Key</b>
- Click <b>Paste Public Key</b>
- Paste your public key, <b>~/.oci/oci_api_key_public.pem</b> into the field
- Click <b>Add</b>
- Copy the contents of <b>Configuration File Preview</b> to your clipboard, and paste them into a file on your machine called <b>\~/.oci/config</b>. The name and location of this file is reserved for OCI. Be sure to update the value that gets assigned to key_file with the path to your private key file (by default, <b>\~/.oci/oci_api_key.pem</b>)

#### Step 5. Set the appropriate permissions on certain files
<pre>
chmod 600 ~/.oci/oci_api_key.pem
chmod 600 ~/.oci/config
</pre>

#### Step 6. Query for and return your Object Storage namespace
- Note that your OCI user would need sufficient OCI IAM privileges to return this information.
- Your Object Storage namespace should be equivalent to the name of your tenancy.which should be equivalent to the name of your tenancy.
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

#### Step 7. Query for all of the users in your tenancy.
- Note that your OCI user would need sufficient OCI IAM privileges to return this information.