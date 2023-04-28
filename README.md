***
## LoopPatches

**README last updated on 28-April-2023**

This branch is modified to work with a proposed addition to Dosing Strategy of a linear ramp associated with Automatic Bolus.

Draft pull requests (Loop 1988 and LoopKit 477) were submitted on 28-April-2023.

This branch of LoopPatches should only be used by experienced testers. There will be updates.

* The primary purpose is to provide easy access to this linear ramp addition
* The secondary purpose is to provide LoopPatches that work in coordination with the code changes in the two pull requests

Summary Instructions.

1. Start with a clean clone of LoopWorkspace, (main or dev as of last test on 28-April-2023) and stash any modifications you want to keep
2. Apply the patch that adds the linear ramp option to AB dosing strategy
    * Inside Loop Settings, there is a new toggle to enable the linear ramp of the bolus partial application factor
        * This strategy uses a minGlucose threshold calculated from the lower bound of your current correction range plus 10 md/dL
        * For glucose at minGlucose and below, a bolusPartialApplicationFactor of 15% is used
        * For glucose at minGlucose to 250 mg/dL, the bolusPartialApplicationFactor ramps up linearly to 100%
        * For glucose greater than 250 mg/dL, the bolusPartialApplicationFactor remains 100%
3. (Optional) Apply the LoopPatches from this branch on top of the version with the new dosing strategy
    * These patches are not required to test the new linear ramp and should not be used by anyone not already familiar with [CustomTypeOne LoopPatches](https://www.loopandlearn.org/custom-type-one-loop-patches)
    * The Dosing Strategy of Automatic Bolus with Linear Ramp enabled is meant to replace the old "Switcher Patch" which has been removed from the branch of LoopPatches
    * The ability to choose bolusPartialApplicationFactor value in LoopPatches remains, but it is a constant over all glucose values that replaces the default 40%, and is only used when you disable the Linear Ramp option
    * The other LoopPatches features are unchanged - please refer to the documentation link above

This README file is bare-bones.

* If you have not used LoopPatches before - do not use
* If you are not keeping up with zulipchat - do not use
* If you have not read every post in this topic [Dosing Strategy Linear Ramp](https://loop.zulipchat.com/#narrow/stream/144182-development/topic/Dosing.20Strategy.20Linear.20Ramp) - do not use

You use these patches at your own risk and **you** are responsible for ensuring they work as you expect. Adjust settings carefully - there are no guardrails or error checking features.

### PR Links

Pay attention to the PR links. There may be changes to the code requested there. Once updates are made at the PR, these patches might be updated without warning.

The two PR for Dosing Strategy Linear Ramp are:

* [Loop #1988](https://github.com/LoopKit/Loop/pull/1988)
* [LoopKit #477](https://github.com/LoopKit/LoopKit/pull/477)

The code changes in these two PR are contained in [PATCH NUMBER ONE](#patch-number-one)

### Copy and Paste Commands

NOTE: These are meant to be applied in the LoopWorkspace folder and are using the new format developed for GitHub patching. (Same commands work in build_loop.yml or for Mac-Xcode build.)

Copy the lines below by hovering the mouse near the top right side of the text and clicking the copy icon. When you click the icon, a message that says “Copied” will appear on your screen.

#### PATCH NUMBER ONE:

Add linear ramp toggle to Dosing Strategy.

```
curl https://raw.githubusercontent.com/marionbarker/LoopPatches/patches-new-dosing-strategy/LoopWorkspace-add-dosing-strategy.patch | git apply
```

There should be no error messages in response (unless there is an error). Make sure you do not see `error: patch failed` at the beginning of a line with various details afterwords. Be sure to only paste one time to a fresh download. A second paste will show errors. To remove this patch, after applying, just replace `git apply` with `git apply --reverse`.

Once applied, the following submodules with show as having been modified:

* Loop
* LoopKit

#### PATCH NUMBER TWO:

OPTIONAL

These patches are compatible with the version of LoopWorkspace with patch number one applied. They are not required to get the linear ramp working. They are only here for those people who want to have LoopPatches still working for their builds.

```
curl https://raw.githubusercontent.com/marionbarker/LoopPatches/patches-new-dosing-strategy/LoopPatch.txt | git apply --directory="Loop"
curl https://raw.githubusercontent.com/marionbarker/LoopPatches/patches-new-dosing-strategy/LoopkitPatch.txt | git apply --directory="LoopKit"
```


#### Build from LoopWorkspace Terminal Window

Copy and paste the next line into the same **LoopWorkspace Terminal Window**. This command (`xed`) will open (or bring to front if already open) Xcode so you can build the code with LoopPatches applied. The `.` just means to open Xcode in the current LoopWorkspace location.

```
xed .

```

* Optional, you can check the modified files inside Xcode using [(Optional) Examine Code Before Building](#optional-examine-code-before-building)


### Build with LoopPatches

1. Click on the Build Arrow to build to the simulator again
    * If a window pops up asking if you want to Replace "Loop"?, click on Replace
    * Confirm the build succeeded without errors
1. (Optional) If you want other customizations, add them now and test build to the simulator again
    * Confirm the build succeeded without errors
1. Plug in your phone, select your phone and build to your phone

### Congratulations!

So long as you did not get any errors, you have now applied the LoopPatches customization to your Loop app. Remember, these are configured under iOS -> Settings -> Loop screen. These features cannot be adjusted inside the Loop app settings screen.

Only enable the feature(s) you want to use and test. Leave the rest disabled.

Every time you build, please check the settings: refer to [Use iOS Settings](#use-ios-settings).

## Confirm Patches Work

For each patch you enable, please confirm it works the way you expect it to work before allowing it to run "unattended". You should check this each time you build. It is too easy to miss a step and have something not operating the way you expect.

If you do not know how to confirm a feature is working, **do not enable** that feature.

There are no guardrails to check Threshold values entered in the patch settings. Please check entries carefully and confirm expected behavior after making a change in patch settings.

### Example for Basal Lock

**Be very careful with this one. If you set the Basal Lock Threshold too low, it will prevent Loop from restricting basal and can cause low glucose.**

**Look for an opportunity when glucose is high, plan on focusing on your phone for 15 to 30 minutes.**

1. Confirm that Closed Loop is enabled and Basal Lock is disabled
1. Next time glucose has been elevated for a while and Loop has given enough insulin that a Temp Basal of 0 U/hr is being provided (raising your correction range can make this happen sooner but be sure to restore it when done testing)
1. Type a value under iOS -> Settings -> Loop -> BASAL LOCK -> Basal Lock Threshold that is less than your current glucose
    * Enable the BASAL LOCK feature
    * Next CGM reading should indicate Loop restored scheduled basal
1. Immediately after that Loop cycle, raise the Basal Lock Threshold to be above your current glucose
    * Next CGM reading should indicate Loop restored lower basal

Now that you've confirmed the patch is working as desired:

* Disable the feature
* Modify the Basal Lock Threshold to the desired value
* Enable the feature (if desired)

Loop will no longer restrict basal when your glucose is higher than this threshold. Pay attention over the next few meals.

* **Start with 250 mg/dL (13.9 mmol/L) or higher**
* **Do not go below 200 mg/dL (11.1 mmol/L) without careful thought and observation**
* **If you notice an increase in lows, raise the threshold or disable the feature**

## (Optional) Examine Code Before Building

In the Xcode window, left pane, you will notice the letter M appears by modified files.

These files should be modified. If they are not, you did not apply the patches successfully. Note the line numbers are all after the patch has been applied. In your Xcode display, when viewing that file, you will see a vertical blue bar left of the code indicating a line has been modified.

1. Under the Loop folder icon (left side of Xcode pane)
    * Loop/Managers/DoseMath.swift
        * line 12, near 395, near 503
    * Loop/Managers/LoopDataManager.swift
        * near line 1599, near 1641
    * LoopUI/Charts
        * These 4 files contain code for the "now" marker
        * COBChart.swift, DoseChart.swift, IOBChart.swift and PredictedGlucoseChart.swift
1. Under the LoopKit folder icon (left side of Xcode pane)
    * LoopKit/InsulinKit/InsulinMath.swift
        * near line 43 and 93
1. If you are comfortable with git command line tools and happen to type git status in the Loop folder, you will notice that `Loop.xcodeproj/project.pbxproj` has been modified and a new Settings.bundle folder with files inside it has been added to the Loop folder.
