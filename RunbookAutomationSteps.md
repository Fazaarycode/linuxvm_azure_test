# Create the Resource Manager template

https://raw.githubusercontent.com/Fazaarycode/linuxvm_azure_test/main/StorageAccountTemplate.json

# Use PowerShell to create an Azure Storage file share and upload the TemplateTest.json file

# Steps:

# Log into Azure
Connect-AzAccount

# -------------------- Uploading to FileShare Starts --------------------

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'bicboi' -Name 'bicboisa'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'bicboisa' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "ct.json" is the path where you saved the ct.json file

$templateFile = './ct.json'

Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile


# -------------------- Uploading to FileShare Ends --------------------




# -------------------- Create the PowerShell runbook script starts --------------------

https://raw.githubusercontent.com/Fazaarycode/linuxvm_azure_test/main/PS_runbookScript.ps1


# Get Key
$key = Get-AzStorageAccountKey -ResourceGroupName 'bicboi' -Name 'bicboisa'


# Powershell - Import Params and Publish Runbook
$importParams = @{
     Path = '/home/mohamed/DeployTemplate.ps1'
      ResourceGroupName ='bicboi'
      AutomationAccountName = 'bicboiautomationtest'
     Type = 'PowerShell'
 }
 # Import Added Values
 Import-AzAutomationRunbook @importParams
  
 # Publish as per params
 $publishParams = @{
     ResourceGroupName = ‘bicboi’
     AutomationAccountName = ‘bicboiautomationtest’
     Name = 'DeployTemplate'
 }
 
 
 Publish-AzAutomationRunbook @publishParams



# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'bicboi'
    StorageAccountName = 'bicboisa'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'ct.json'
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'bicboi'
    AutomationAccountName = 'bicboiautomationtest'
    Name = 'DeployTemplate' 
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams





# Deploy Another


$runbookParams = @{
    ResourceGroupName = 'bicboi'
    StorageAccountName = 'bicboisecondct'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json'
}


$startParams = @{
    ResourceGroupName = 'bicboi'
    AutomationAccountName = 'bicboiautomationtest'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

$job = Start-AzAutomationRunbook @startParams

