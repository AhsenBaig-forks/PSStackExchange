# Source
https://ramblingcookiemonster.github.io/Building-A-PowerShell-Module/

## zModuleBuild.ps1
```
# We cloned our project to C:\sc\PSStackExchange
$Path = 'C:\sc\PSStackExchange'
$ModuleName = 'PSStackExchange'
$Author = 'RamblingCookieMonster'
$Description = 'PowerShell module to query the StackExchange API'

# Create the module and private function directories
mkdir $Path\$ModuleName
mkdir $Path\$ModuleName\Private
mkdir $Path\$ModuleName\Public
mkdir $Path\$ModuleName\en-US # For about_Help files
mkdir $Path\Tests

#Create the module and related files
New-Item "$Path\$ModuleName\$ModuleName.psm1" -ItemType File
New-Item "$Path\$ModuleName\$ModuleName.Format.ps1xml" -ItemType File
New-Item "$Path\$ModuleName\en-US\about_$ModuleName.help.txt" -ItemType File
New-Item "$Path\Tests\$ModuleName.Tests.ps1" -ItemType File
New-ModuleManifest -Path $Path\$ModuleName\$ModuleName.psd1 `
                   -RootModule $ModuleName.psm1 `
                   -Description $Description `
                   -PowerShellVersion 3.0 `
                   -Author $Author `
                   -FormatsToProcess "$ModuleName.Format.ps1xml"

# Copy the public/exported functions into the public folder, private functions into private folder
```
## zModuleBuild.Publish.ps1
```
# This assumes you are running PowerShell 5

# Parameters for publishing the module
$Path = 'C:\SC\PSStackExchange\PSStackExchange'
$PublishParams = @{
    NuGetApiKey = 'REDACTED!' # Swap this out with your API key
    Path = 'C:\SC\PSStackExchange\PSStackExchange'
    ProjectUri = 'https://github.com/RamblingCookieMonster/PSStackExchange'
    Tags = @('StackExchange', 'StackOverflow', 'REST', 'API' )
}

# We install and run PSScriptAnalyzer against the module to make sure it's not failing any tests
Install-Module -Name PSScriptAnalyzer -force
Invoke-ScriptAnalyzer -Path $Path

# ScriptAnalyzer passed! Let's publish
Publish-Module @PublishParams

# The module is now listed on the PowerShell Gallery
Find-Module PSStackExchange
``` 

## zModuleBuild.PSStackExchange.ps1
```
# Install and import the module
    Install-Module PSStackExchange
    Import-Module PSStackExchange

# Get commands in the module
    Get-Command -Module PSStackExchange

# Get help
    Get-Help Get-SEObject -Full
    Get-Help about_PSStackExchange

# Find Stack Exchange Sites and Urls
    Get-SEObject -Object Sites |
        Sort -Property api_site_parameter |
        Select -Property api_site_parameter, site_url

# Search questions on stack overflow
#   Tagged PowerShell
#   with System.DBNull in the title
    Search-SEQuestion -Title System.DBNull -Tag PowerShell -Site StackOverflow

# Search questions on ServerFault
#   Posted by User with ID 105072
#   With PowerShell in the Title
#   Without the tag windows-server-2008-r2
    Search-SEQuestion -User 105072 `
                      -Site ServerFault `
                      -ExcludeTag 'windows-server-2008-r2' `
                      -Title PowerShell
```
