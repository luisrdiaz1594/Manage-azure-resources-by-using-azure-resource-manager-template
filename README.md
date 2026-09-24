# Project – Manage Azure Resources with Azure Resource Manager (ARM) Templates

![Azure Resource Manager](https://img.shields.io/badge/Azure-Resource%20Manager-0078D4?style=flat&logo=microsoftazure&logoColor=white)
![Bicep](https://img.shields.io/badge/IaC-ARM%20%7C%20Bicep-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-success)
![AZ-104](https://img.shields.io/badge/Cert%20Prep-AZ--104-orange)

---

## Overview

This project walks through deploying Azure managed disks using **Infrastructure as Code (IaC)**. A single disk was created in the Azure Portal, its configuration was exported as an ARM template, and that template was then edited and redeployed four different ways: the Portal's custom deployment editor, Azure PowerShell, Azure CLI, and Bicep.

The end result is **five managed disks** in one resource group, each deployed from the same template logic with different tools, showing that infrastructure can be defined once and deployed repeatably.

Based on the Microsoft Learning **AZ-104 Lab 03 – Manage Azure resources by using Azure Resource Manager templates**, following the walkthrough by Gbenga Adekunle.

---

## Environment

| Tool | Purpose |
|------|---------|
| Azure Portal | Disk creation, template export, custom deployment |
| ARM Templates (JSON) | Declarative resource definitions |
| Azure Cloud Shell (PowerShell) | `New-AzResourceGroupDeployment`, `Get-AzDisk` |
| Azure Cloud Shell (Bash) | `az deployment group create`, `az disk list` |
| Bicep | Simplified IaC language that compiles to ARM |
| GitHub | Lab files source and documentation |

---

## Resources Deployed

| Disk | Deployment Method | SKU | Size |
|------|-------------------|-----|------|
| az104-disk1 | Azure Portal (GUI) | Standard_LRS | 32 GiB |
| az104-disk2 | Portal custom deployment (edited ARM template) | Standard_LRS | 32 GiB |
| az104-disk3 | Azure PowerShell | Standard_LRS | 32 GiB |
| az104-disk4 | Azure CLI | Standard_LRS | 32 GiB |
| az104-disk5 | Bicep via Azure CLI | StandardSSD_LRS | 32 GiB |

**Resource group:** `RG3AZ104` | **Region:** East US

---

## Task 1 – Create a Managed Disk and Export the ARM Template

**Actions Taken:**
1. Created resource group `RG3AZ104` and managed disk `az104-disk1` in East US (32 GiB, Standard HDD LRS, no redundancy, empty source)
2. Confirmed the deployment succeeded
3. Opened **Automation → Export template** on the disk
4. Downloaded both the template file and the parameters file

![Create Managed Disk](task1-01-create-managed-disk.png)
*Creating az104-disk1 in the Azure Portal*

![Deployment Complete](task1-02-deployment-complete.png)
*Portal deployment of az104-disk1 completed successfully*

![Export Template](task1-03-export-template.png)
*Exporting the ARM template for az104-disk1*

![Export Parameters](task1-04-export-parameters.png)
*Exported parameters file, both JSON files downloaded locally*

---

## Task 2 – Edit the ARM Template and Redeploy in the Portal

**Actions Taken:**
1. Loaded the template into **Deploy a custom template → Build your own template in the editor**
2. Renamed the parameter `disks_az104_disk1_name` to the generic `disk_name` (2 occurrences)
3. Changed the default value from `az104-disk1` to `az104-disk2`
4. Loaded and edited the parameters file to match the new parameter name
5. Deployed to `RG3AZ104` and verified both disks and both deployments

![Edit Template Parameter](task2-01-edit-template-parameter-rename.png)
*Renaming the auto-generated parameter to a reusable name*

![Edit Template Disk Name](task2-02-edit-template-disk2-name.png)
*Updating the default disk name to az104-disk2*

![Edit Parameters](task2-03-edit-parameters.png)
*Updating the parameters file to match the renamed parameter*

![Custom Deployment Complete](task2-04-custom-deployment-complete.png)
*Custom template deployment succeeded*

![Resource Group Two Disks](task2-05-rg-two-disks.png)
*RG3AZ104 now contains az104-disk1 and az104-disk2*

![Deployment History](task2-06-deployment-history.png)
*Resource group deployment history showing both successful deployments*

---

## Task 3 – Deploy with Azure PowerShell

**Actions Taken:**
1. Opened Cloud Shell (PowerShell) and uploaded the template and parameters files
2. Edited the template in the Cloud Shell editor, setting the default disk name to `az104-disk3`
3. Deployed with:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName RG3AZ104 -Name az104lab3 -TemplateFile "template (1).json" -TemplateParameterFile "parameters (1).json"
```

4. Verified all disks with `Get-AzDisk`

![Upload Parameters](task3-01-cloudshell-upload-parameters.png)
*Uploading the parameters file to Cloud Shell*

![Upload Template](task3-02-cloudshell-upload-template.png)
*Uploading the template file to Cloud Shell*

![Edit Template Disk3](task3-03-edit-template-disk3.png)
*Setting the default disk name to az104-disk3 in the Cloud Shell editor*

![PowerShell Deploy](task3-04-powershell-deploy.png)
*First attempt failed validation; the rerun with deployment name az104lab3 succeeded*

![PowerShell Deploy Output](task3-05-powershell-deploy-output.png)
*Deployment output confirming the parameter value az104-disk3*

![Get-AzDisk Disk1](task3-06-get-azdisk-disk1.png)
*Get-AzDisk output: 32 GB, 500 IOPS, 60 MBps, Unattached*

![Get-AzDisk Disk1 ID](task3-07-get-azdisk-disk1-id.png)
*az104-disk1 resource details*

![Get-AzDisk Disk2](task3-08-get-azdisk-disk2.png)
*az104-disk2 resource details*

![Get-AzDisk Disk3](task3-09-get-azdisk-disk3.png)
*az104-disk3 created by the PowerShell deployment*

![Resource Group Three Disks](task3-10-rg-three-disks.png)
*RG3AZ104 now contains three disks*

---

## Task 4 – Deploy with Azure CLI

**Actions Taken:**
1. Switched Cloud Shell to Bash
2. Edited the template default disk name to `az104-disk4`
3. Deployed with:

```bash
az deployment group create --resource-group RG3AZ104 --name az104lab4 --template-file "template (1).json" --parameters "@parameters (1).json"
```

4. Verified with:

```bash
az disk list --resource-group RG3AZ104 --output table
```

![Bash Edit Template](task4-01-bash-edit-template-disk4.png)
*Cloud Shell in Bash with the template set to az104-disk4*

![CLI Deploy](task4-02-cli-deploy.png)
*Azure CLI deployment of az104lab4*

![CLI Deploy Output](task4-03-cli-deploy-output.png)
*Deployment output showing az104-disk4 as the output resource*

![CLI Disk List](task4-04-cli-disk-list.png)
*az disk list confirming four disks provisioned*

![Resource Group Four Disks](task4-05-rg-four-disks.png)
*RG3AZ104 now contains four disks*

---

## Task 5 – Deploy with Bicep

**Actions Taken:**
1. Downloaded the lab files from the [MicrosoftLearning AZ-104 repo](https://github.com/MicrosoftLearning/AZ-104-MicrosoftAzureAdministrator)
2. Uploaded `azuredeploydisk.bicep` to Cloud Shell
3. Edited the Bicep file:
   - `managedDiskName` default → `az104-disk5`
   - `sku` name → `StandardSSD_LRS`
   - `diskSizeGB` → `32`
4. Deployed with:

```bash
az deployment group create --resource-group RG3AZ104 --name az104lab5 --template-file "azuredeploydisk.bicep"
```

5. Verified all five disks

![GitHub Download ZIP](task5-01-github-download-zip.png)
*Downloading the AZ-104 lab files from GitHub*

![Upload Bicep](task5-02-upload-bicep.png)
*Uploading azuredeploydisk.bicep to Cloud Shell*

![Bicep Parameters](task5-03-bicep-parameters.png)
*Bicep parameters with decorators (@description, @minValue, @maxValue) and disk name az104-disk5*

![Bicep SKU](task5-04-bicep-sku.png)
*SKU changed to StandardSSD_LRS*

![Bicep Disk Size](task5-05-bicep-disksize.png)
*diskSizeGB set to 32*

![Bicep Deploy](task5-06-bicep-deploy.png)
*Bicep deployment az104lab5 with linter warnings*

![CLI Disk List Five](task5-07-cli-disk-list-five.png)
*az disk list showing az104-disk5 as StandardSSD_LRS*

![Resource Group Five Disks](task5-08-rg-five-disks.png)
*Final state: five managed disks in RG3AZ104*

---

## Skills Demonstrated

| Skill | How It Was Applied |
|-------|--------------------|
| Infrastructure as Code | Defined and redeployed disks from templates instead of manual clicks |
| ARM Template Authoring | Exported, parameterized, and edited JSON templates |
| Template Parameterization | Replaced auto-generated parameter names with reusable ones |
| Azure PowerShell | Deployed with `New-AzResourceGroupDeployment`, verified with `Get-AzDisk` |
| Azure CLI | Deployed with `az deployment group create`, verified with `az disk list` |
| Bicep | Edited and deployed a Bicep file with parameter decorators |
| Troubleshooting | Resolved deployment name validation and CLI argument errors |
| Deployment Auditing | Tracked deployment history and correlation IDs per resource group |

---

## Lessons Learned

**Exported templates need cleanup before reuse.** Azure generates parameter names tied to the original resource (`disks_az104_disk1_name`) and leaves the parameter file value as `null`. Renaming the parameter to something generic makes the template reusable for any disk.

**Deployment names have strict rules.** My first PowerShell deployment failed because the name didn't match Azure's allowed pattern (letters, numbers, `-`, `.`, `_`, and parentheses only). Using a clean name like `az104lab3` fixed it.

**Read the CLI errors.** `az disk list` without `--resource-group` returned a required-argument error. Adding `-g RG3AZ104` returned the expected table.

**Linter warnings point to real issues.** The Bicep deployment warned that `diskSizeinGiB` was declared but never used, because I hardcoded `diskSizeGB: 32`. The cleaner fix is to set the parameter's default to 32 and keep `diskSizeGB: diskSizeinGiB`, so the value stays configurable. The BCP081 warning came from an older API version (`2020-09-30`) without type definitions, which didn't block deployment.

**Incremental mode is the default.** Every deployment ran in Incremental mode, so new disks were added without touching existing resources. Complete mode would have deleted anything in the resource group not defined in the template.

**Cloud Shell storage can be ephemeral.** This session didn't persist files, so templates need to be re-uploaded or stored in a mounted file share for future sessions.

---

## References

- [Microsoft Learning – AZ-104 Lab Files](https://github.com/MicrosoftLearning/AZ-104-MicrosoftAzureAdministrator)
- [ARM Template Documentation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/)
- [Bicep Documentation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/)
- [Azure Managed Disks Overview](https://learn.microsoft.com/en-us/azure/virtual-machines/managed-disks-overview)
- [Lab walkthrough video by Gbenga Adekunle](https://youtu.be/QIoQ3OwPTWc)
