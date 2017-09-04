## PowerShell Design Guidelines


### Keep the code consistent in terms of casing, syntax and vocabulary.
   * Always code as if the guy who ends up maintaining your code will be a
     violent psychopath who knows where you live.

### If writing .ps1 code, keep as much content as possible in methods
   * Have just one method call at the end of the code - a kind of int main(void).

### Method naming
   * Use the verbs from the Get-Verb
   * Windows PowerShell recommends that cmdlet and function names have the Verb-Noun
     format and include an approved verb.
     This practice makes command names consistent, predictable and easier to use,
     especially for users who do not speak English as a first language.

### Use of parentheses and param definition
   * Use CamelCasing naming for function parameters.
   * Use camelCasing naming for internal function parameters.
   * Use the opening curly bracket on the first line where function name is and use
     the closing bracket on the new line after the code block ends { #\n add a newline here } .
   * Add a single space between variable and operator.
   * Use lower case language keywords and identifiers.
   * Example:

   ```powershell
   function Start-VMSpecial {
       param($MyAwesomeParam)
       # leave a space here 
       $myInternalParam = "MyParam"
       if ($myInternalParam -eq "MyParam1") {
           Write-Host "Not bad 2"
       } else {
           Write-Host "Not bad"
       }
   }
   ```

### Error control
   * Use ActionPreference to Stop, so that the failed cmdlets can be caught by try/catch blocks.

   ```powershell
   $ErrorActionPreference = "Stop"
   ```
   * Always use $LastExitCode checks when you run binaries.
   * Add retries to all executions that can have transient failures (network access/disk access/etc):
     https://github.com/cloudbase/windows-openstack-imaging-tools/blob/master/WinImageBuilder.psm1#L42

### Modularizing the code
   * Use a *.psd1 file to declare your module
   * Use one or more *.psm1 files to declare your common methods.
   * Try to use only entry point (a .ps1) which imports a module and executes one static method.
   * From the *.psm1 export the minimum amount of methods and define them in the *.psd1 file.
     This way, the module will have a well defined interface and the backwards compatibility
     can be easily controlled.
   * Good example: https://github.com/cloudbase/juju-powershell-modules/tree/master/JujuHelper

### Documentation
   * Add a SYNOPSIS for every method

   ```powershell
   function Get-MyPrecious {
       <#
       .SYNOPSIS
       Returns a precious value
       #>
       return "precious"
   }
   ```

### Unit testing
   * You will need Pester on your system. It should already be installed on your system
     if you are running Windows 10. If it is not:

   ```powershell
   Install-Package Pester
   ```
   * Run the tests without polluting your current shell environment:

   ```cmd
   cmd /c powershell.exe -NonInteractive {Invoke-Pester}
   ```
   * If on GitHub, you can integrate the PowerShell repo to have automated the unit tests
     run with https://www.appveyor.com/
   * Example appveyor config:
     https://github.com/cloudbase/juju-powershell-modules/blob/master/appveyor.yml


### Code quality
   * PSScriptAnalyzer can be used to check the PowerShell code for quality.
   * Example:

   ```powershell
   Install-Module -Name PSScriptAnalyzer
   $rules = @("PSProvideCommentHelp","PSUseDeclaredVarsMoreThanAssignment",
              "PSAvoidUsingEmptyCatchBlock","PSAvoidUsingCmdletAliases",
              "PSAvoidDefaultValueForMandatoryParameter",
              "PSAvoidDefaultValueSwitchParameter",
              "PSUseToExportFieldsInManifest","PSAvoidUsingPositionalParameters")
   Invoke-ScriptAnalyzer -Path . -IncludeRule $rules
   ```
   * Try to keep the maximum line length to 79 chars.
     It makes the commits and pull requests more readable.
