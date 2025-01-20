# Define the OU structure
$parentOUName = "InfraIT_Users"
$childOUName = "Finance"
$domainPath = "DC=infrait,DC=sec"

# Function to create an OU with error handling
function Create-ADOU {
    param (
        [string]$Name,
        [string]$Path
    )
    
    try {
        if (-not(Get-ADOrganizationalUnit -Filter "Name -eq '$Name'" -SearchBase $Path)) {
            New-ADOrganizationalUnit -Name $Name -Path $Path
            Write-Host "Successfully created OU: $Name" -ForegroundColor Green
            return $true
        } else {
            Write-Host "OU already exists: $Name" -ForegroundColor Yellow
            return $true
        }
    } catch {
        Write-Host "Failed to create OU: $Name" -ForegroundColor Red
        Write-Host "Error: $_" -ForegroundColor Red
        return $false
    }
}

# Create parent OU
$parentCreated = Create-ADOU -Name $parentOUName -Path $domainPath

# If parent was created successfully, create child OU
if ($parentCreated) {
    $parentPath = "OU=$parentOUName,$domainPath"
    Create-ADOU -Name $childOUName -Path $parentPath
}