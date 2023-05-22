***
## LoopPatches

**README last updated on 22-May-2023**

This branch applies a patch for a new option when selecting Automatic Bolus Dosing Strategy. The option can be enabled on the Dosing Strategy screen and is only active when Automatic Bolus is selected. The percentage of the recommended bolus amount that is automatically delivered with each Loop cycle ramps from 20% to 80% depending on Glucose level compared to the lower value in your Correction Range.

* Option is similar in speed of correction to Temp Basal when Glucose is near correction range
* Option is similar to the fixed 40% correction of Automatic Bolus when Glucose in slightly elevated above correction range
* Option provides 80% correction with Automatic Bolus when Glucose is elevated (above 200 mg/dL, 11.1 mmol/L)

This branch works with dev as of LoopWorkspace commit 0ea1acf, 2023-May-21.

The associated PR are (Loop 1988 and LoopKit 477).

This branch of LoopPatches should only be used by experienced testers. There will be updates. We started calling this a "linear" ramp, but it will probably be renamed later.

If you have a version of Loop with CustomTypeOne LoopPatches installed, you must first remove them. See [Remove CustomTypeOne LoopPatches](#remove-customtypeone-looppatches).

### Summary Instructions

1. Start with a clean clone of LoopWorkspace main branch
2. Apply [PATCH NUMBER ONE](#patch-number-one) to add this new option to AB dosing strategy
    * Inside Loop Settings, Dosing Strategy, this patch adds a new toggle to enable the "linear ramp" of the bolus partial application factor
        * This strategy uses a minGlucose threshold calculated from the lower bound of your current correction range plus 10 md/dL
        * For glucose at minGlucose and below, a bolusPartialApplicationFactor of 20% is used
        * For glucose in the range of minGlucose to 200 mg/dL, the bolusPartialApplicationFactor increases linearly to 80%
        * For glucose greater than 200 mg/dL, the bolusPartialApplicationFactor remains 80%
        * The "linear ramp" option is disabled by default
3. (Optional) Apply [PATCH NUMBER TWO](#patch-number-two) to add a compatible version of [CustomTypeOne LoopPatches](https://www.loopandlearn.org/custom-type-one-loop-patches)
    * These patches are not required to test the new linear ramp and should not be used by anyone not already familiar with them
    * The Dosing Strategy of Automatic Bolus with Linear Ramp enabled is meant to replace the old "Switcher Patch" which has been removed from this branch of LoopPatches
    * The ability to choose bolusPartialApplicationFactor value in LoopPatches remains: as before, it is a constant over all glucose values that replaces the default 40%, but is only used when you disable the new "Linear Ramp" option
    * The other LoopPatches features are unchanged - please refer to the documentation link above

This README file is bare-bones.

* This is meant for experienced people willing to test new code
* If you are not keeping up with zulipchat - do not use
* If you have not read every post in this topic [Dosing Strategy Linear Ramp](https://loop.zulipchat.com/#narrow/stream/144182-development/topic/Dosing.20Strategy.20Linear.20Ramp) - do not use

You use these patches at your own risk and **you** are responsible for ensuring they work as you expect. Adjust settings carefully - there are no guardrails or error checking features.

### PR Links

Pay attention to the PR links. There may be changes to the code requested there. Please be aware these PR may be updated for dev branch.

* If you are using Loop main branch - use this patch file with the branch name dosing-strategy-ramp-main

* If you are using Loop dev branch - use this patch file with the branch name dosing-strategy-ramp-dev
  * Some changes to Loop dev will require patch updates - please be patient 

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
curl https://raw.githubusercontent.com/marionbarker/LoopPatches/dosing-strategy-ramp-dev/LoopWorkspace-add-dosing-strategy.patch | git apply
```

There should be no error messages in response. Make sure you do not see `error: patch failed` at the beginning of a line with various details afterwords. Be sure to only paste one time to a fresh download. A second paste will show errors. To remove this patch, after applying, just replace `git apply` with `git apply --reverse`.

Once applied, the following submodules with show as having been modified:

* Loop
* LoopKit

#### PATCH NUMBER TWO:

OPTIONAL

These patches are compatible with the version of LoopWorkspace with patch number one applied. They are not required to get the linear ramp working. They are only here for those people who want to have LoopPatches still working for their builds.

```
curl https://raw.githubusercontent.com/marionbarker/LoopPatches/dosing-strategy-ramp-dev/LoopPatch.txt | git apply --directory="Loop"
curl https://raw.githubusercontent.com/marionbarker/LoopPatches/dosing-strategy-ramp-dev/LoopkitPatch.txt | git apply --directory="LoopKit"
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

In the Xcode window, left pane, you will notice the letter M appears by modified files. Some are in Loop and some in LoopKit.

## Remove CustomTypeOne LoopPatches

If you have the main branch of CustomTypeOne LoopPatches already installed they must first be removed before any of these new patches can be used.

This is true if you added LoopPatches yourself or if you selected Loop with Patches using the Build Select Script.

Go to the LoopWorkspace folder and issue these commands to remove CustomTypeOne LoopPatches main branch from your clone.

```
curl https://raw.githubusercontent.com/CustomTypeOne/LoopPatches/main/LoopPatch.txt | git apply --reverse --directory=Loop
curl https://raw.githubusercontent.com/CustomTypeOne/LoopPatches/main/LoopkitPatch.txt | git apply --reverse --directory=LoopKit

```

Then return to [Summary Instructions](#summary-instructions) to apply the new patches.