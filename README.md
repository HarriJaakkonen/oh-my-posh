<!-- markdownlint-disable -->
<p align="center">
  <img
    width="400"
    src="https://raw.githubusercontent.com/jandedobbeleer/oh-my-posh/main/website/static/img/logo.png"
    alt="Oh My Posh logo – Prompt theme engine for any shell"
  />
</p>
<!-- markdownlint-enable -->

![MIT license badge](https://img.shields.io/github/license/JanDeDobbeleer/oh-my-posh.svg)

![Build Status badge](https://img.shields.io/github/actions/workflow/status/jandedobbeleer/oh-my-posh/release.yml?branch=main)

[![Release version number badge][release-badge]][release]

[![Documentation link badge ohmyposh.dev][docs-badge]][docs]

![Number of GitHub Downloads badge](https://img.shields.io/github/downloads/jandedobbeleer/oh-my-posh/total?color=pink&label=GitHub%20Downloads)

<!-- markdownlint-disable -->
<div align="center" markdown="1">
   <sup>Special thanks to:</sup>
   <br>
   <br>
   <a href="https://www.warp.dev/oh-my-posh">
      <img alt="Warp sponsorship" width="400" src="https://github.com/user-attachments/assets/c21102f7-bab9-4344-a731-0cf6b341cab2">
   </a>

### [Warp, the intelligent terminal for developers](https://www.warp.dev/oh-my-posh)
[Available for MacOS, Linux, & Windows](https://www.warp.dev/oh-my-posh)<br>

</div>
<hr>
<!-- markdownlint-enable -->

This repo was made with love using GitKraken.

[![GitKraken shield][kraken]][kraken-ref]
<!-- markdownlint-disable first-header-h1 -->

## Join the community

![Mastodon badge](https://img.shields.io/mastodon/follow/110275292073181892?domain=https%3A%2F%2Fhachyderm.io&label=Mastodon&style=social)

![Discord badge](https://img.shields.io/discord/1023597603331526656)

What started as the offspring of [oh-my-posh2](https://github.com/JanDeDobbeleer/oh-my-posh2) for PowerShell
resulted in a cross platform, highly customizable and extensible prompt theme engine. After 4 years of working
on oh-my-posh, a modern and more efficient tool was needed to suit my personal needs.

## :heart: Support :heart:

[![Swag][swag-badge]][swag] - Show your love with a t-shirt!

[![GitHub][github-badge]][github-sponsors] - One time support, or a recurring donation?

[![Ko-Fi][kofi-badge]][kofi] - No coffee, no code.

<!-- markdownlint-disable -->
<a href="https://polar.sh/oh-my-posh">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://polar.sh/embed/tiers.svg?org=oh-my-posh&darkmode">
    <img alt="Subscription Tiers on Polar" src="https://polar.sh/embed/tiers.svg?org=oh-my-posh">
  </picture>
</a>
<!-- markdownlint-enable -->

## Features

* Shell and platform agnostic
* Easily configurable
* The **most** configurable prompt utility
* Fast
* Secondary prompt
* Right prompt
* Transient prompt

## Documentation

[![Documentation][docs-badge]][docs]

## Reviews

* [Repo review](https://repo-reviews.github.io//reviews/2023-06-21_TameWizard_JanDeDobbeleer_oh-my-posh) by [TameWizard](https://github.com/TameWizard)

## Thanks

* [Chris Benti](https://github.com/chrisbenti/PS-Config) providing the first influence to start oh-my-posh
* [Keith Dahlby](https://github.com/dahlbyk/posh-git) for creating posh-git and making life more enjoyable
* [Robby Russell](https://github.com/ohmyzsh/ohmyzsh) for creating oh-my-zsh, without him this would probably not be here
* [Janne Mareike Koschinski](https://github.com/justjanne) for providing information on how to get certain information
using Go (and the amazing [README](https://github.com/justjanne/powerline-go))
* [Starship](https://github.com/starship/starship/blob/master/src/init/mod.rs) for doing great things

[kraken]: https://img.shields.io/badge/GitKraken-Legendary%20Git%20Tools-teal?style=plastic&logo=gitkraken
[kraken-ref]: https://www.gitkraken.com/invite/nQmDPR9D
[swag-badge]: https://img.shields.io/badge/Swag-Get%20some!-blue
[swag]: https://swag.ohmyposh.dev
[github-badge]: https://img.shields.io/badge/-Sponsor-fafbfc?logo=GitHub%20Sponsors
[github-sponsors]: https://github.com/sponsors/JanDeDobbeleer
[kofi-badge]: https://img.shields.io/badge/Ko--fi-Buy%20me%20a%20coffee!-%2346b798.svg
[kofi]: https://ko-fi.com/jandedobbeleer
[docs-badge]: https://img.shields.io/badge/Docs-ohmyposh.dev-blue
[docs]: https://ohmyposh.dev
[release-badge]: https://img.shields.io/github/v/release/jandedobbeleer/oh-my-posh?label=Release
[release]: https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest

## Microsoft Security Theme

![Microsoft Security Theme]

A Microsoft Security–inspired theme with Azure blue highlights, Entra ID tenant info, subscription context, and admin status indicator. Perfect for security engineers and admins who live in Entra, Azure, and Git.

Need the following script to $profile

function Update-OMPContext {
  param(
    [int]$RefreshBeforeExpiryMinutes = 5
  )

  $ctxDir  = Join-Path $env:LOCALAPPDATA 'oh-my-posh'
  $ctxPath = Join-Path $ctxDir 'context.json'
  $old     = @{}

  if (Test-Path $ctxPath) {
    try { $old = Get-Content $ctxPath -Raw | ConvertFrom-Json -ErrorAction Stop } catch { $old = @{} }
  }

  # --- 1) Current Azure account signature (cheap) -----------------------------
  $az = az account show --output json 2>$null | ConvertFrom-Json
  if (-not $az) {
    # Not signed in; publish neutral values and persist
    $env:OMP_CLOUD_USER      = ''
    $env:OMP_AZ_NAME         = ''
    $env:OMP_TENANT_SHORT    = ''
    $env:OMP_TOKEN_REMAINING = 'No Session'
    $env:OMP_ROLE            = 'Standard User'
    New-Item -ItemType Directory -Force -Path $ctxDir | Out-Null
    @{ signature = ''; role = 'Standard User'; expiresOn = '' } | ConvertTo-Json | Set-Content -Path $ctxPath -Encoding UTF8
    return
  }

  $signature = '{0}|{1}|{2}' -f $az.tenantId, $az.name, $az.user.name

  # --- 2) Token remaining (only compute when needed) -------------------------
  $token      = $null
  $tokenLeft  = 'No Token'
  $expiresOn  = $old.expiresOn

  $needToken = $true
  if ($old.expiresOn) {
    try {
      $expOld = [datetimeoffset]::Parse($old.expiresOn)
      $needToken = ($expOld - [datetimeoffset]::UtcNow).TotalMinutes -le $RefreshBeforeExpiryMinutes
    } catch { $needToken = $true }
  }

  if ($needToken) {
    $token = az account get-access-token --resource https://graph.microsoft.com/ --output json 2>$null | ConvertFrom-Json
    if ($token) {
      $expiresOn = $token.expiresOn
      $exp  = [datetimeoffset]::Parse($token.expiresOn)
      $rem  = $exp - [datetimeoffset]::UtcNow
      if ($rem.TotalMinutes -le 0) { $tokenLeft = 'Expired' }
      elseif ($rem.TotalHours -ge 1) { $tokenLeft = '{0}h {1}m' -f [int][math]::Floor($rem.TotalHours), [int][math]::Floor($rem.TotalMinutes % 60) }
      else { $tokenLeft = '{0}m' -f [int][math]::Ceiling($rem.TotalMinutes) }
    }
  } else {
    $tokenLeft = $old.OMP_TOKEN_REMAINING
  }

  # --- 3) Role (only when signature changed or role missing) -----------------
  $role = $old.role
  $needRole = ([string]::IsNullOrWhiteSpace($role)) -or ($old.signature -ne $signature)

  if ($needRole) {
    try {
      $memberOf = az rest --method GET --url https://graph.microsoft.com/v1.0/me/memberOf --output json 2>$null | ConvertFrom-Json
      $roles = @()
      foreach ($i in $memberOf.value) {
        if ($i.'@odata.type' -eq '#microsoft.graph.directoryRole') { $roles += $i.displayName }
      }
      if (-not $roles -or $roles.Count -eq 0) { $role = 'Standard User' }
      elseif ($roles -contains 'Global Administrator') { $role = 'Global Administrator' }
      elseif ($roles -contains 'Security Administrator') { $role = 'Security Administrator' }
      elseif ( ($roles -join ',') -match 'Privileged' ) { $role = 'Privileged' }
      elseif ( ($roles -join ',') -match 'Administrator' ) { $role = 'Administrator' }
      else { $role = 'Standard User' }
    } catch { $role = 'Standard User' }
  }

  # --- 4) Publish env vars for the prompt (cloud-first) ----------------------
  $env:OMP_CLOUD_USER      = ($az.user.name ?? '').Trim()
  $env:OMP_AZ_NAME         = ($az.name ?? '').Trim()
  $env:OMP_TENANT_ID       = $az.tenantId
  $env:OMP_TOKEN_REMAINING = $tokenLeft
  $env:OMP_ROLE            = $role

  # --- 5) Persist small cache so next shell start is instant ------------------
  New-Item -ItemType Directory -Force -Path $ctxDir | Out-Null
  @{
    signature           = $signature
    role                = $role
    expiresOn           = $expiresOn
    OMP_TOKEN_REMAINING = $tokenLeft
  } | ConvertTo-Json | Set-Content -Path $ctxPath -Encoding UTF8
}

# --- Ensure OMP context is updated before every prompt render -----------------
if (-not ($function:prompt -match "Update-OMPContext")) {
  $originalPrompt = $function:prompt
  function prompt {
    Update-OMPContext
    & $originalPrompt
  }
}

#    then update once per session (for initial load):
Update-OMPContext

# 3) OPTIONAL: refresh the context automatically after an 'az account set' / 'az login'
Register-EngineEvent PowerShell.Exiting -Action { } | Out-Null  # placeholder to keep $ExecutionContext.Events alive
$ExecutionContext.InvokeCommand.CommandNotFoundAction = {
  param($commandName, $commandLookupEventArgs)
  if ($commandName -eq 'az') { return }
}
function global:az {
  param([Parameter(ValueFromRemainingArguments = $true)] $Args)
  Microsoft.Azure.CLI $Args
  if ($Args -and $Args[0] -eq 'account' -and ($Args -contains 'set' -or $Args -contains 'clear' -or $Args -contains 'login' -or $Args -contains 'logout')) {
    Update-OMPContext
  }
}

oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH\microsoft-security.omp.json" | Invoke-Expression

