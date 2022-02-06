# BuildImmersion's (planned) single asic build

A single loop immersion system for S19s or M30s (or equivalent asics) with a focus on build quality (reliability), temperature and protection control (able to run unsupervised "forever"), negligible noise (slightly oversized heat exchanger / fan so that the fan will never need to run on max), flexible placement (inside or outside, heat exchanger placed by tank or externally), heat reuse as is (air) or connected to water heating system by water-to-water heat exchanger.

Detailed library of documentation can be found [here](https://keybase.pub/buildimmersion/) as system is getting as-built (continually updated).

* [DCX ThermaSafe R dielectric fluid](https://cryptocooling.eu/#fluid), two 25l containers: $482 ($241 per container, $9.64 per litre)
* Tank will be custom made. Drawings can be found [here](https://keybase.pub/buildimmersion/Tank%20Drawings/). Estimated cost: ~$700
* Circulator pump to be determinded (TBD), but will start out with a used [Grundfos Alpha2 25-80 180 (~$250, 50W)](https://product-selection.grundfos.com/products/alpha/alpha2/alpha2-25-80-180-98649757?tab=variant-curves) that I came across, read off flow and power parameters from its display, and calculate needed pump size from there. I estimate that a variant of [Grundfos Magna3](https://product-selection.grundfos.com/products/magna/magna3?tab=models) is needed: ~$600, 100-200W
* [16x16" heat exchanger](https://www.outdoorfurnacesupply.com/16x16-water-to-air-heat-exchanger-hot-water-coil-outdoor-wood-furnace.html): $149
* Custom made fan shroud for above heat exchanger. Drawings can be found [here](https://keybase.pub/buildimmersion/Fan%20Shroud%20Drawings/). Estimated cost: ~$250
* [350mm axial fan](https://ventilatorry.ru/downloads/ebmpapst/datasheet/s3g350-ag03-52-en-datasheet-ebmpapst.pdf). Purchased directly from manufacturer (ebm-papst): ~$350, <85W
* [PID 0-10v speed controller for fan](https://www.alliedelec.com/product/red-lion-controls/pxu40030/71103615/): ~$140
* Other electrical and control components: ~$700
* Miscellaneous: ~$500

Total: ~$3871 + shipping etc.

Power consumption (estimated): 150W

# Single Asic Immersion System Example Build With Parts List

### PID Regulator (Automatic Fan Speed Controller)
Keep the [manual](https://www.redlion.net/sites/default/files/213/6305/PXU%20Manual_0.pdf) at hand when setting up this not-intuitive PID controller. I spent a few hours familiarizing myself with it, hopefully this will help you save some time:
* Enter the "Hidden Loop" by holding the P button for 3 seconds
* Continue pressing the P button until it says "CNFP", press the up arrow
* Welcome to the settings, sorted in groups (or "modules") from 1 to 9. Browse the groups with the up and down arrows. Exit to the main menu (the "Display Loop") at any time by pressing the D button.
* If you do anything wrong and aren't sure how to correct it, see page 33 of the above mentioned manual (restore factory settings - I used it on several occasions)

Here are my settings (browse each module setting with the up and down arrow, enter and move to the next parameter with the P button):

Module 1 - Input Parameters (1-IN):
* tYPE: r385      Standard PT100 temperature element
* SCAL: Celsius   
* dCPt: 0.0       Kept at default
* FLtr: 8         Kept at default
* bANd: 1.0       Kept at default
* SHFt: 0.0       Kept at default (even though I have a 2-wire PT100 element)
* SPLO: 20.0      Arbitrarily set to 20C as a safety measure in case of a user error in extremely cold weather
* SPHI: 60.0      Arbitrarliy set to 60C as a safety measure so that the miner doesn't boil
* F1In: r-S       Here you can set what you want the F1 button on the controller to do. I figured it would be useful to have it as a start/stop button.
* F2In: trnF      As above, but for the F2 button. I chose to have it toggle between auto (PID regulation) and manual so I can play around and force the fan speed when I want to.

Module 2 - Output Parameters (2-OP):
* OPAC: d1        
* CtRL: PId       
* StPt: SP        
* CYCl: 2.0       Kept at default
* OP1L: 0.0       Consider setting this to 30 as the minimum speed for fans are usually 30% to coole the electric motor
* OP1H: 100.0     
* IF01: 0.0       Kept at default
* An1L: 0.0       
* An1H: 100.0     
* CHYS: 2.0       Kept at default

Module 3 - Lockout Parameters (3-LC), or which parameters to see on your display and where:
* SP: dISP        Set Point changeable from the main menu ("Display Loop"). Consider moving this or hiding it if you don't want others to change it easily
* OP1: dISP       I moed this up to the main menu ("Display Loop") so I can easily see the output level (fan speed in %)
* OP2: PArA       N/A (kept as default)
* SPrP: HIdE      Hid it, as I won't use it
* PId: HIdE       Hid it, as I won't use it
* r-S: HIdE       Hid it, as I use it as my F1 key function
* PSt: PArA       N/A (kept as default)
* r-ti: PArA      N/A (kept as default)
* ProF: HIdE      Hid it, as I won't use it
* PSEG: LOC       Kept as default
* Pind: dISP      
* OPOF: PArA      N/A (kept as default)
* ProP: PArA      Nice to have here if you wanna tune the PID controller
* Intt: PArA      Nice to have here if you wanna tune the PID controller
* dERt: PArA      Nice to have here if you wanna tune the PID controller
* dInt: LOC       Kept as default
* AL-1: PArA      N/A (kept as default)
* AL-2: PArA      Kept as default
* ALrS: PArA      Kept as default
* SPSL: HIdE      Hid it, as I won't use it
* tUNE: HIdE      Kept as default
* CtrL: HIdE      Kept as default
* StPt: LOC       Kept as default
* trnF: HIdE      Kept as default
* dEV: LOC        Locked it, as I won't use it
* SP1: dISP       
* SP2: LOC        Kept as default
* CodE: 0         

Module 4 - Alarm Parameters (4-AL):

You should at least
