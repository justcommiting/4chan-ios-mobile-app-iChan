# GitHub Actions Workflows

## Build iOS IPA

This workflow automatically builds an iOS IPA file for the iChan application.

### Triggers

The workflow runs on:
- Push to `main` or `master` branches
- Pull requests to `main` or `master` branches
- Manual trigger via workflow_dispatch

### What it does

1. Checks out the code
2. Sets up Flutter (version 3.3.10)
3. Installs project dependencies
4. Installs CocoaPods dependencies
5. Builds the iOS app (without code signing by default)
6. Creates an IPA file
7. Uploads the IPA as an artifact (available for 30 days)

### Downloading the IPA

After the workflow runs successfully:
1. Go to the Actions tab in GitHub
2. Click on the workflow run
3. Scroll down to the "Artifacts" section
4. Download the `iChan-IPA` artifact

### Setting up Code Signing (Optional)

To create a properly signed IPA that can be installed on devices, you need to set up code signing:

1. **Export your Apple Developer certificates and provisioning profile:**
   ```bash
   # Export certificate to p12 file (you'll be prompted for a password)
   # In Keychain Access: Right-click certificate > Export
   
   # Convert to base64
   base64 -i certificate.p12 | pbcopy
   ```

2. **Add GitHub Secrets:**
   - Go to your repository Settings > Secrets and variables > Actions
   - Add the following secrets:
     - `IOS_CERTIFICATE_BASE64`: The base64-encoded .p12 certificate
     - `IOS_CERTIFICATE_PASSWORD`: The password for the .p12 file
     - `IOS_PROVISION_PROFILE_BASE64`: Base64-encoded provisioning profile

3. **Uncomment the code signing steps** in `build-ipa.yml`:
   - Uncomment the "Install Apple Certificate" step
   - Uncomment the "Install Provisioning Profile" step
   - Remove `--no-codesign` from the build command

### Notes

- The IPA built without code signing can be used with tools like AltStore
- For App Store distribution, you'll need proper code signing
- The workflow uses macOS runners which have limited free minutes on GitHub Actions

### Troubleshooting

**Build fails with Flutter version issues:**
- Update the `flutter-version` in the workflow to match your project requirements

**Pod install fails:**
- Check if there are any git dependencies in pubspec.yaml that require SSH access
- You may need to add deploy keys for private repositories

**Build takes too long:**
- Consider caching Flutter dependencies and CocoaPods
- Optimize by only building on specific branches or tags
