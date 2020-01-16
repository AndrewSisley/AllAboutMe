# Sin list

This file contains a list of coding mistakes that I have made and subsequently discovered since I started this list on approximately 2019-11-22.  I started it because I was sick of not having a good overview of the bugs I had introduced, and why I introduced them.  Hopefully it will help me spot patterns in my failures and adjust the way I work accordingly.  Pull requests highly appreciated.

Type | Identifier | Date | Description | Cause
---- | ---------- | ---- | ----------- | -----
Bug - external | GEC-3377 | 2019-9-25 | Colors not being calculated correctly in Widget | Added new complication to system that was being calculated in 2 different ways without fixing that first, also missed a test case
Bug - internal | GEC-4118 | 2019-11-22 | Widget displayed incorrect value (1% off) in new system | Failed to check internals of called methods, failed to accurately check displayed number
Bug - internal | GEC-4181 | 2019-11-22 | SQL migration from old to new system didn't check for existing new records | Missed a use case
Bug - internal | GEC-4124 | 2019-11-22 | SQL migration from old to new system didn't account for old implicit | Missed implicit to explicit conversion
Bug - internal | GEC-4320 | 2019-12-16 | Special widget lost its special legend after adding new system in | Failed to test special widget adding new system in.  Special code was handled in giant if-else block making it hard to spot.
Bug - internal | GEC-4325 | 2019-12-16 | Google Maps widget legend lost it's marker labels after adding new system | Failed to spot prop was used (I actually checked this widget for this as I thnought it might be used, but some how missed it (fatigue/time-presure?) and dismissed the widget as being irrelevant to the changes made)
Bug - internal | GEC-4397 | 2019-12-20 | Colors not being calculated in widget extension | Complex, fiddly requirements for a special case.  However I spotted the bug during release testing pretty quickly, likely testing was rushed in order to merge before Christmas. No unit tests where there should have been.
Bug - internal | GEC-4397 | 2019-12-20 | Special colors lost from widget extension composed of reworked extension | Failed to check for composition cases when using reflection based dependency resolution. Bug only present in legacy location that I failed to test.
