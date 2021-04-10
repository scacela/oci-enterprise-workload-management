# Setup Instructions for OCI-CLI

#### Step 1. Create the directory whose name and location is reserved for OCI-CLI.
<pre>
mkdir ~/.oci
</pre>

#### Step 2. Create an API signing key pair
The following command will create an API signing key pair (i.e. 1 x public, 1 x private) in <b>\~/.oci</b>.
<pre>
oci setup keys
</pre>

#### Step 3. Register the public API signing key to your User page in OCI

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

#### Step 4. Set the appropriate permissions on certain files
<pre>
chmod 600 ~/.oci/oci_api_key.pem
chmod 600 ~/.oci/config
</pre>

#### Step 5. See if you can query for and return your Object Storage namespace, which should be equivalent to the name of your tenancy. Note that your OCI user would need sufficient OCI IAM privileges to return this information.
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

#### Step 6. See if you can list all of the users in your tenancy. Again, note that your OCI user would need sufficient OCI IAM privileges to return this information.
oci iam user list