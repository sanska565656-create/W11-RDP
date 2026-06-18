name: Windows 11 Free RDP (Auto Info)

on: 
  workflow_dispatch: # Isse aap manually run kar sakte ho

jobs:
  build:
    runs-on: windows-latest

    steps:
    - name: Download and Install Tailscale
      run: |
        Write-Host "Installing Tailscale..." -ForegroundColor Cyan
        Invoke-WebRequest -Uri https://pkgs.tailscale.com/stable/tailscale-setup-latest-amd64.msi -OutFile tailscale-setup.msi
        Start-Process msiexec.exe -ArgumentList '/i tailscale-setup.msi /qn /norestart' -Wait

    - name: Connect to Tailscale
      env:
        TAILSCALE_AUTH_KEY: ${{ secrets.TAILSCALE_AUTH_KEY }}
      run: |
        Write-Host "Connecting to Tailscale Network..." -ForegroundColor Cyan
        & "C:\Program Files\Tailscale\tailscale.exe" up --authkey=$env:TAILSCALE_AUTH_KEY --accept-routes

    - name: Enable Remote Desktop (RDP)
      run: |
        Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0
        Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
        
    - name: Set Custom Password for Runner
      run: |
        $Password = ConvertTo-SecureString "KingRDP@2026!" -AsPlainText -Force
        Set-LocalUser -Name "runneradmin" -Password $Password

    - name: Display Connection Details
      run: |
        # Tailscale IP nikalne ke liye
        $TS_IP = (& "C:\Program Files\Tailscale\tailscale.exe" ip -4)
        
        Write-Host "`n==================================================" -ForegroundColor Green
        Write-Host "🎉 RDP IS READY TO CONNECT! 🎉" -ForegroundColor Green
        Write-Host "==================================================" -ForegroundColor Green
        Write-Host "📍 IP ADDRESS : $TS_IP" -ForegroundColor Yellow
        Write-Host "👤 USERNAME   : runneradmin" -ForegroundColor Yellow
        Write-Host "🔑 PASSWORD   : KingRDP@2026!" -ForegroundColor Yellow
        Write-Host "==================================================" -ForegroundColor Green
        Write-Host "Note: This session will auto-close after 6 hours.`n"

    - name: Keep Alive Loop (6 Hours)
      run: |
        for ($i = 1; $i -le 360; $i++) {
          Start-Sleep -Seconds 60
        }
        
