# Enterprise Grade Workload Management Guide
Examples, references and set-up instructions for smoother operations.

## Table of Contents

- [OCI API Guide](#oci-api-guide)
- [Basic Tips and Tricks](#basic-tips-and-tricks)
- [Developer Tools](#developer-tools)
- [OCI Information](#oci-information)
- [Network Connectivity Options](#network-connectivity-options)
- [Free OCI Features](#free-oci-features)
- [Shell Operations](#shell-operations)
- [API Operations with OCI-CLI and Shell Scripting](#api-operations-with-oci-cli-and-shell-scripting)
- [Scheduled API Operations with OCI-CLI](#scheduled-api-operations-with-oci-cli)


## OCI API Guide
For users who are looking to interact with and leverage Oracle Cloud APIs to their best advantage.

### SDKs

- [SDKs - Oracle Docs](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/sdks.htm) | Official Oracle docs covering SDK and CLI guides

### OCI-CLI

- [Quickstart - Oracle Docs](https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/cliinstall.htm#Quickstart) | Official Oracle docs covering set-up of OCI-CLI

- [Quickstart - Fast](./OCI-CLI/setup-instructions.md) | Instructions for rapid set-up of OCI-CLI

- [Compute Instance Lab](./OCI-CLI/compute-lab.md) | Lab covering OCI-CLI basics. Deploy a Compute Instance via OCI-CLI!

### Terraform
- [Setup - Oracle Docs](https://docs.oracle.com/en-us/iaas/developer-tutorials/tutorials/tf-provider/01-summary.htm) | Official Oracle docs covering set-up of OCI Terraform

## Basic Tips and Tricks

### How to search for information efficiently
- [Google search](https://www.lifehack.org/articles/technology/20-tips-use-google-search-efficiently.html)

### Keyboard shortcuts
- [MacOS](https://support.apple.com/en-us/HT201236) | See "Document Shortcuts" section for CLI shortcuts

- [Windows](https://support.microsoft.com/en-us/windows/keyboard-shortcuts-in-windows-dcc61a57-8ff0-cffe-9796-cb9706c75eec) | See "Command Prompt keyboard shortcuts" section for CLI shortcuts

- [Microsoft Word shortcuts for Mac OS](https://support.microsoft.com/en-us/office/keyboard-shortcuts-in-word-95ef89dd-7142-4b50-afb2-f762f663ceb2#PickTab=macOS) |Select your OS from the tabs on this page to see shortcuts specific to your OS

### Basic CLI commands
- [Unix-based](http://mally.stanford.edu/~sr/computing/basic-unix.html)

- [Microsoft Powershell](https://devblogs.microsoft.com/scripting/table-of-basic-powershell-commands/)

## Developer Tools

- [Sublime Text](https://www.sublimetext.com/) | Text editor for programming

- [Atom](https://atom.io/) | Text editor for programming

 - [jq](https://stedolan.github.io/jq/) | CLI tool used for processing JSON

## OCI Information

- [Oracle home](https://www.oracle.com/index.html)

- [Customer Successes](https://www.oracle.com/customers/)

- [Documentation](https://docs.oracle.com/en/)

- [Architecture Center](https://docs.oracle.com/solutions/?type=reference-architectures&page=0&is=true&sort=0)

- [Getting-Started Labs](https://github.com/oracle/learning-library/tree/master/oci-library) | Oracle Learning Library

## Network Connectivity Options

<b>FastConnect</b> and <b>VPN Connect</b> are two kinds of network connectivity options. FastConnect involves the use of a physical cable.

The 3 FastConnect connectivity models can be found [here](https://www.oracle.com/cloud/networking/fastconnect-connectivity-models.html). They are:

1. Established via physical connection to an Oracle Provider
2. Established via colocation with Oracle
3. Established via physical connection to a Third-Party Provider

VPN Connect best practices for network connectivity as of Feb 2020 are shown in [this Oracle Whitepaper](https://docs.cloud.oracle.com/en-us/iaas/Content/Resources/Assets/whitepapers/ipsec-vpn-best-practices.pdf), which shows architectures modeled with consideration of design principles with VPN Connect, and VPN Connect over FastConnect. The architectures shown here do not require an <b>Internet Gateway</b>, but use a <b>Dynamic Routing Gateway</b>.

## Free OCI Features

<b>Events</b> and <b>Functions</b> are two kinds of resources that you can use to automate Tenancy management processes (using code deployments in Functions) based on state changes of resources (which can be tracked by Events) throughout your Tenancy.

<b>Notifications</b> is a resource that can be used to broadcasts messages to you (e.g. by email).

<b>Monitoring</b> is a service that encompasses metric querying charts, and the Alarm resource.

<b>Health Checks</b> is a resource that enables external monitoring to determine availability and performance of public facing resources.

<b>Tagging</b> is a service that enables you to add (key, value)-style metadata to your resources to help you manage resources by this metadata.
Budgets is a resource that can be used to notify you by email when you reach a spending quantity that you define for a set of resources that you define.

## Shell Operations

### Send SSH keys from local to remote
<pre>
host=opc@&lthostip&gt;
path=/home/opc;
ssh_key_path=~/.ssh/gromacs_id_rsa;
scp -i ${ssh_key_path} -r ~/.ssh ${host}:${path}
</pre>

### Repetitive output (Unix-based OS)
e.g. Some action (print the time) occurs 60 times with frequency of 1/1.0s
<details>
<summary>
Unix-based OS
</summary>

<pre>
cooldown=1.0
maxreps=60
for i in $(seq 1 ${maxreps}); do echo -e $i $(date '+%H:%M:%S%1N')'\nsleep' ${cooldown}s'\n';  sleep ${cooldown}s; done;
</pre>
</details>

<details>
<summary>
Microsoft Powershell
</summary>

<pre>
1..60 | %{echo "$_ $(Get-Date -Format HH:mm:ss.fff)"; echo "sleep 1.0`n"; sleep 1.0}
</pre>
</details>

### Get commands for Bash colors
<details>
<summary>
Linux
</summary>

<pre>
for i in {31..37}; \
do echo -e "\e[${i}mecho -e '\\\e[${i}m'"; \
done; echo -e "\e[0mecho -e '\\\e[0m' #DEFAULT"
</pre>
</details>

<details>
<summary>
Mac OS (or older Bash)
</summary>

<pre>
for i in {31..37}; \
do echo -e "\033[0;${i}mecho -e '\\\033[0;${i}m'"; \
done; echo -e "\033[0mecho -e '\\\033[0m' #DEFAULT"
</pre>
</details>

## API Operations with OCI-CLI and Shell Scripting
This section includes sets of instructions that can be used to perform relatively complex tasks. The dependencies for running most of these sets of instructions are:
- A Unix-based OS
- oci-cli
- jq (for installation instructions, please refer to the [Developer Tools](#developer-tools) section.

### OCI API Profile template
A prerequisite for using OCI-CLI. Default location: <b>\~/.oci/config</b>. The following example is a template for a default profile, using the reserved header: <b>[DEFAULT]</b>
<pre>
[DEFAULT]
user=YOUR_USER_OCID
fingerprint=YOUR_FINGERPRINT
Tenancy=YOUR_TENANCY_OCID
region=YOUR_REGION_IDENTIFIER
key_file=PATH_TO_API_PRIVATE_KEY_FILE
</pre>

### Get Tenancy S3 Compartment OCID for AWS API compatibility
Dependencies: oci-cli, jq
<pre>
oci os ns get-metadata | jq '.data."default-s3-compartment-id"' | sed 's/\"//g'
</pre>

### Get Tenancy Swift Compartment OCID for SWIFT API compatibility
Dependencies: oci-cli, jq
oci os ns get-metadata | jq '.data."default-swift-compartment-id"' | sed 's/\"//g'

### Get Tenancy namespace
Dependencies: oci-cli, jq
oci os ns get-metadata | jq .data.namespace | sed 's/\"//g'

### List Region Identifiers in Tenancy
Dependencies: oci-cli, jq
oci iam region list | jq .data[].name | sed 's/\"//g'

### List Region Keys and Identifiers in Tenancy
Dependencies: oci-cli, jq
<pre>
echo -e "key  identifier\n---------------"; oci iam region list | jq '.data[] | .key + "  " + .name' | sed 's/\"//g'
</pre>

### Get Availability Domains (ADs) in a Region
Dependencies: oci-cli, jq

e.g. In region us-ashburn-1
<pre>
region=us-ashburn-1
oci iam availability-domain list --region ${region} | jq .data[].name | sed 's|"\(.*\)"|\1|g'
</pre>

### Upload file to Object Storage
Dependencies: oci-cli

e.g. Upload file ~/Desktop/file1 to bucket large_files
<pre>
bucket_name=large_files
path_of_upload_file=~/Desktop/file1
oci os object put -bn ${bucket_name} --file ${path_of_upload_file}
</pre>

### Upload folder contents to Object Storage
Dependencies: oci-cli

e.g. Upload contents of folder ~/Desktop/folder1 to bucket large_files
<pre>
bucket_name=large_files
path_of_upload_folder=~/Desktop/folder1
oci os object bulk-upload --src-dir ${path_of_upload_folder} -bn ${bucket_name}
</pre>

### Get image name given image OCID and Region
e.g. In region us-ashburn-1, find the name associated with a sample image ocid

<details>
<summary>
Dependencies: oci-cli, jq
</summary>

<pre>
region=us-ashburn-1
image_id=ocid1.image.oc1.iad.aaaaaaaaatmaaxweorpdp6uumrj7neogyu5uqs34qp4acq4opsullqxnxmmq
oci compute image --region ${region} get --image-id ${image_id} | jq '.data."display-name"'
</pre>
</details>

<details>
<summary>
Dependencies: oci-cli
</summary>

<pre>
region=us-ashburn-1
image_id=ocid1.image.oc1.iad.aaaaaaaaatmaaxweorpdp6uumrj7neogyu5uqs34qp4acq4opsullqxnxmmq
oci compute image --region ${region} get --image-id ${image_id} | grep display-name
</pre>
</details>

### Get VCN OCID given VCN display name, Compartment OCID and Region
Dependencies: oci-cli, jq

e.g. get ocids of vcns whose display names contain string: _
region=us-ashburn-1
<pre>
compartment_id=COMPARTMENT_OCID
oci network --region ${region} vcn list --compartment-id ${compartment_id} | jq '.data[] | select(."display-name"|contains("_")).id + "    " + ."display-name"' | sed 's/"//g'
</pre>
### List all compute instance OCIDs given display name across all Regions
Dependencies: oci-cli, jq

e.g. get ocids of computes whose display names contain string: <b>samuel</b>
<pre>
color_1="\033[36m" # Mac OS color. Newer Bash equivalent: "\e[36m"
color_reset="\033[0m" # Mac OS color. Newer Bash equivalent: "\e[0m"
search_regions=($(oci iam region list | jq .data[].name | sed 's/\"//g'));
for i in "${search_regions[@]}"; do echo -e "\n${color_1}${i}${color_reset}"; oci search --region $i resource free-text-search --text "samuel" | jq '.data[][].identifier | select(contains("ocid1.instance."))'; done
</pre>

### List all compute instance OCIDs given Defined Tag value across up to all regions and up to all compartments
Dependencies: oci-cli, jq

e.g. Search specific Compartments (or search all Compartments) and the following Regions:
- us-ashburn-1
- us-phoenix-1
- ap-osaka-1
for compute instances were created by someone whose name contains the string: <b>samuel</b>
<pre>
tag_namespace=Oracle-Tag
tag_key=CreatedBy
export tag_value=samuel
# search across all regions
# regions=($(oci iam region list | jq .data[].name | sed 's/\"//g'))
# search across fewer regions
regions=(us-ashburn-1 us-phoenix-1 ap-osaka-1) # add to list as needed

# <b>search across all compartments</b>

# uncomment the next line
# compartments=($(oci iam compartment list --compartment-id-in-subtree true | jq .data[].id | sed 's/\"//g'));

# <b>OR use the Compartment OCID of the first Compartment whose name contains the string: samuel</b>

# uncomment the next two lines
compartment_id=$(export name=samuel; oci iam compartment list --compartment-id-in-subtree true | jq '.data[] | select(.name|contains(env.name)).id + "    " + .name' | sed 's/\"//g' | head -1 | awk '{print $1}')
# compartments=(${compartment_id})

# <b>OR use your own Compartment OCID</b>

# uncomment the next two lines and replace COMPARTMENT_OCID with your own Compartment OCID
# compartment_id=COMPARTMENT_OCID
# compartments=(${compartment_id})

# colors
color_region="\033[36m" # Mac OS color. Newer Bash equivalent: "\e[36m"
color_compartment="\033[35m" # Mac OS color. Newer Bash equivalent: "\e[35m"
color_alternating="\033[33m" # Mac OS color. Newer Bash equivalent: "\e[33m"
color_reset="\033[0m" # Mac OS color. Newer Bash equivalent: "\e[0m"
# for each region
for i in ${regions[@]}; do
echo -e "${color_region}${i}${color_reset}" # print the region identifier
# for each compartment
for j in ${compartments[@]}; do
# get the name of the current compartment and print it along with the compartment id
compartment_name=$(oci iam compartment get --compartment-id $j | jq .data.name | sed 's/\"//g')
echo -e "${color_compartment}$j    ${compartment_name}${color_reset}";
compute_list=()
# read in each line of output from a command, where each line contains a compute id and compute display name
while IFS= read -r line; do
compute_list+=("${line}") # store each line as an array element
done < <( oci compute --region $i instance list --compartment-id $j | jq --arg tag_namespace "${tag_namespace}" --arg tag_key "${tag_key}" '.data[] | select(."defined-tags" != null ) | select(."defined-tags"[$tag_namespace] != null ) | select(."defined-tags"[$tag_namespace][$tag_key] |contains(env.tag_value)).id + "    " + ."display-name"' | sed 's/\"//g')
# for each array element, i.e. each line, print the line with alternating colored text
for ((c=0;c<${#compute_list[@]};c++)); do
if [ $(($c%2)) -eq 0 ]; then echo -en "${color_alternating}"; fi; echo -e "${compute_list[$c]}${color_reset}"
done; done; done
</pre>

### List all Compartment names in Tenancy
Dependencies: oci-cli, jq
<pre>
oci iam compartment list --compartment-id-in-subtree true | jq .data[].name
</pre>

### List all Users in Tenancy
Dependencies: oci-cli, jq
<pre>
oci iam user list | jq .data[].name
</pre>

### Get Compartment OCIDs in Tenancy given compartment display name
Dependencies: oci-cli, jq

e.g. Get ocids of compartments whose display names contain string: <b>samuel</b>
<pre>
export name=samuel
oci iam compartment list --compartment-id-in-subtree true | jq '.data[] | select(.name|contains(env.name)).id + "    " + .name' | sed 's/\"//g'
</pre>

### Get Compute Instance details given Compute Instance OCID and Region
Dependencies: oci-cli

e.g. In region us-ashburn-1
<pre>
compute_id=COMPUTE_INSTANCE_OCID
region=us-ashburn-1
oci compute --region ${region} instance get --instance-id ${compute_id}
</pre>

### Create a Console Connection to access a Compute Instance given Compute Instance OCID, Region and SSH public key path
Dependencies: oci-cli

e.g. In region us-ashburn-1
<pre>
region=us-ashburn-1
compute_id=COMPUTE_INSTANCE_OCID
ssh_public_key_path=~/.ssh/id_rsa.pub;
oci compute --region ${region} instance-console-connection create --instance-id ${compute_id} --ssh-public-key-file ${ssh_public_key_path}
</pre>

### Delete a Console Connection in a given Region
Dependencies: oci-cli

e.g. In region us-ashburn-1
<pre>
region=us-ashburn-1
connection_id=CONSOLE_CONNECTION_OCID
oci compute --region ${region} instance-console-connection delete --instance-console-connection-id ${connection_id}
</pre>

### Start/Stop/Reset/Softstop/Softreset a compute instance in a given region
Dependencies: oci-cli

e.g. Softreset a compute instance in region us-ashburn-1
<pre>
region=us-ashburn-1
compute_id=COMPUTE_INSTANCE_OCID
action=softreset
oci compute --region ${region} instance action --instance-id ${compute_id} --action ${action}
</pre>

### Start/Stop an Analytics Cloud (OAC) instance in a given Region
Dependencies: oci-cli

e.g. Start an oac instance in region us-ashburn-1
region=us-ashburn-1
<pre>
action=start
oac_id=OAC_INSTANCE_OCID
oci --region ${region} analytics analytics-instance ${action} --analytics-instance-id ${oac_id}
</pre>

### Start/Stop an Autonomous Database (ADB) instance in a given Region
Dependencies: oci-cli

e.g. Stop an ADB instance in region us-ashburn-1
<pre>
region=us-ashburn-1
action=stop
adb_id=ADB_INSTANCE_OCID
oci --region ${region} db autonomous-database ${action} ${adb_id}
</pre>

### Delete a Compute Instance given Compute OCID and Region
Dependencies: oci-cli

e.g. In region us-ashburn-1
<pre>
region=us-ashburn-1
compute_id=COMPUTE_INSTANCE_OCID
oci compute --region ${region} instance terminate --instance-id ${compute_id}
</pre>

### Get OCIDs and display names of Compute Instances that belong to a VCN given VCN OCID, Compartment OCID and Region
Dependencies: oci-cli, jq

e.g. In region us-ashburn-1
<pre>
compartment_id=COMPARTMENT_OCID
export vcn_id=VCN_OCID
region=us-ashburn-1
for sub in $(oci network --region ${region} subnet list --compartment-id ${compartment_id} | jq '.data[] | select(."vcn-id"==env.vcn_id)'.id | sed 's/"//g'); do # for subnets in compartment
export subnet_id=${sub} # set subnet id;
export instance_id=$(oci compute --region ${region} vnic-attachment list --compartment-id ${compartment_id} | jq '.data[] | select(."subnet-id"==env.subnet_id)."instance-id"' | sed 's/\"//g'); # get vnic whose subnet is subnet id
oci compute --region ${region} instance get --instance-id ${instance_id} | jq '.data|.id + "    " + ."display-name"' | sed 's/\"//g' # get instance whose vnic is vnic id;
done;
</pre>

### Get availability for cores of a particular compute shape in a Region
Dependencies: oci-cli, jq

e.g. Get availability of GPU3 cores in region uk-london-1
<pre>
compartment_id=COMPARTMENT_OCID
region=uk-london-1
limit_name=gpu3-count
color_1="\033[36m" # Mac OS color. Newer Bash equivalent: "\e[36m"
color_2="\033[32m" # Mac OS color. Newer Bash equivalent: "\e[32m"
color_reset="\033[0m" # Mac OS color. Newer Bash equivalent: "\e[0m"
array_ads=($(oci iam availability-domain list --region ${region} | jq .data[].name | sed 's/\"//g'))
for ad in ${array_ads[@]}; do
echo -e ${color_1}${ad}${color_2};
oci limits resource-availability get --service-name compute --region ${region} --compartment-id ${compartment_id} --limit-name ${limit_name} --availability-domain ${ad} | jq .data | sed 's/[\"|,|{|}]//g' | sed 's/^[[:space:]]*\(.\)/\1/g';
done; echo -e ${color_reset};
</pre>

## Scheduled API Operations with OCI-CLI

- [Crontab Guru](https://crontab.guru/) | Handy tool for writing crontab entries

### Scheduled Start/Stop a Compute Instance in a given Region, using cron
Dependencies: oci-cli, crontab (Linux utility)

e.g. Stop an instance in region us-ashburn-1 at 1710 (5:10 PM) on Monday-Friday, according to the timezone to which the Compute Instance is set
<pre>
10 17 * * 1-5 oci --region us-ashburn-1 compute instance action --instance-id COMPUTE_INSTANCE_OCID --action stop
# e.g. start an instance in region us-ashburn-1 at 0610 on Monday-Friday
10 6 * * 1-5 oci --region us-ashburn-1 compute instance action --instance-id COMPUTE_INSTANCE_OCID --action start
</pre>

### List/Set timezones
<pre>
timedatectl list-timezones # list
sudo timedatectl set-timezone America/New_York # set (e.g. to New York time)
date # verify change
timedatectl # verify change
</pre>