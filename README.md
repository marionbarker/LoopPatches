***
## LoopPatches

**README last updated on 26-June-2023**

The primary source for these patches has moved. See note below.

These patches are provided open source and it is your responsibility to review the code. These patches are highly experimental for testing, research, and education and are not approved for therapy. It is your responsibility to review the code and understand the changes using this makes. You should also run tests in an xcode simulator to review the effect they have on Loop behavior and settings.

LoopPatches offer several adjustments to Loop. Each is disabled by default the first time you build after adding the patches, but your selected values are maintained for subsequent builds for a given phone. Only enable the feature(s) you want to use and test. Leave the rest disabled.

Documentation found at [LoopPatches](https://www.loopandlearn.org/custom-type-one-loop-patches/). **Read the documentation before applying and using the patches.**

The primary source for these patches has moved. As of 25-June-2023, the dev branch is no longer valid. We cannot maintain these patches in more than one place.

The best method is to use the Customization Select script. Documentation is at [Run Customization Select Script](https://www.loopandlearn.org/custom-code) for both Mac-Xcode and Browser Build methods.

The Customization Select Script automatically handles selecting the correct version should updates be required by changes to dev or released code in the future.

```
/bin/bash -c "$(curl -fsSL \
    https://raw.githubusercontent.com/loopnlearn/loopbuildscripts/main/CustomizationSelect.sh)"
```

The LoopPatches code is lightly tested and **you use this at your own risk**.

There are **no guardrails**, so be extremely careful when you enter thresholds in iOS settings.

If you prefer to select which patch to apply yourself, LoopPatches are not a LoopWorkspace level patch. You must choose which of the 3 options you want to apply from this folder.

https://github.com/loopandlearn/customization/tree/main/customtypeone_looppatches
