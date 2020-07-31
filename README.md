# PowerShell SecretStore module

This module is an extension vault for the PowerShell SecretManagement module.
It stores secrets locally on file for the current user account context, and uses .NET crypto APIs to encrypt file contents.
Secrets remain encrypted in-memory, and are only decrypted when retrieved and passed to the user.
This module works over all supported PowerShell platforms on Windows, Linux, and macOS.
In the default configuration, a password is required to store and access secrets, and provides the strongest protection.  

## Configuration

The SecretStore module allows a number of configurations.  

It can be configured to require a password to unlock the store, or operate without a password.  
The no-password option still encrypts secrets on file and in memory.
But the key for decryption is stored on file in the current user location, and is less secure.  

SecretStore can also be configured to prompt the user for the password if needed.
When a password is provided, it applies only to the current PowerShell session and only for a limited time.
The password timeout time is also configurable and set to 15 minutes by default.
Password prompting is useful when SecretStore is used interactively.
For automation scenarios, password prompting can be disabled.  

If password prompting is disabled and a password is required to access secrets, a Microsoft.PowerShell.SecretStore.PasswordRequiredException will be thrown.
In this case, the SecretStore can be unlocked using the `Unlock-LocalStore` cmdlet.  

There is also a SecretStore `Scope` setting, but it is currently set to `CurrentUser` and cannot be changed.  

The default configuration is set to for best security and interactive use.  

```powershell
Get-LocalStoreConfiguration

      Scope PasswordRequired PasswordTimeout DoNotPrompt
      ----- ---------------- --------------- -----------
CurrentUser             True             900       False
```

### SecretStore cmdlets

The SecretStore exports five cmdlets for manipulating configuration and store state.  

#### Get-LocalStoreConfiguration

This cmdlet displays the current configuration.  

#### Set-LocalStoreConfiguration

This cmdlet sets configuration options for SecretStore.
Individual configuration options can be set, or the store can be configured to default settings by using the `-Default` parameter switch.  

```powershell
Set-LocalStoreConfiguration -PasswordTimeout 30

      Scope PasswordRequired PasswordTimeout DoNotPrompt
      ----- ---------------- --------------- -----------
CurrentUser             True              30       False
```

#### Unlock-LocalStore

This cmdlet unlocks the SecretStore with the provided password.
The password can be passed in as a `SecureString` type or in plain text.  

#### Update-SecretStorePassword

This cmdlet updates the SecretStore password.
It takes no parameters and can only be used interactively, as it prompts the user for old and new passwords.  

#### Reset-SecretStore

This cmdlet deletes all data in the SecretStore, and updates the configuration.
If no configuration parameters are specified, the default settings will be used.  
This cmdlet is intended for cases where the password is forgotten, or store files become corrupt and SecretStore is unusable.
