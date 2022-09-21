## Software

When 3D printing, we are using **Slic3r** as the slicing software, exporting as g-code and then copying to an SD card. The printer can print directly from g-code files on tan SD card.

When directly troubleshooting we are connecting the printer via USB and using Pronterface to send g-code commands over the serial console.

The following custom buttons were added to Pronterface:

* Center Test: _Home the printer, then go to 0,0,1 quickly_
  * `g28`
  * `g0 z1 y0 x0 f8000`
* X Tower: _Go to base of the X tower, 1mm above the plate_
  * `g28`
  * `g0 z1 y-60 x-100 f8000`
* Y Tower: _Go to base of the Y tower, 1mm above the plate_
  * `g28`
  * `g0 z1 y-60 x100 f8000`
* Z Tower: _Go to base of the Z tower, 1mm above the plate_
  * `g28`
  * `g0 z1 y90 x0 f8000`

These buttons simplify the configuration of the Z-stops and Delta Radius (see below). After moving the head to one of the locations, we use the 0.1mm -Z button in Pronterface to move the print head 10 steps until it pinches a piece of paper.

## Hardware

We are currently using a **FLSUN Kossel Delta** 3D printer. It is a [Delta Robot](https://en.wikipedia.org/wiki/Delta_robot), with roller glides for the three towers.

It was built from a kit and has several known issues:

- The auto leveling is not reliable, possibly due to shifting of the print head while it senses the bed height. We switched to manual adjustment of the delta parameters to get consistent results. We don't use the bed leveling function.
- The nozzle tends to clog, which we believe is due to an air gap between the heat break and the nozzle, which causes a glob of molten plastic to sit in the filament path.
- We are not sure if the filament feed rate is correct, or if restrictions in the print head are causing problems. On longer print jobs, the extruder motor will struggle to feed filament through the system.

And the following resolved issues:

- Play in the ball joints. The screws that hold the ball joint balls into the nylon carriers were loose. To fix, a small amount of plastic (waste PLA strands) was stuffed into the unthreaded bolt holes to tighten them up. A better fix would be to rebuild or reprint the carriages with room for capture nuts, or use nut inserts to allow the bolts to be torqued down.
- Belt tension. One tower had a looser belt, which was tightened by one tooth.
- Play in the carriage wheels. This is adjustable and was adjusted to minimize movement outside the vertical range.

### Calibration

This section is for software adjustments. Some additional information can be found in the [Marlin Firmware G-code Documentation](https://marlinfw.org/meta/gcode/)

#### Z-Stops adjustment

The Z-Stops are located at the top of each axis pillar. We can set an offset for each one so that the printer firmware can compensate for variations in the lengths of the pillars.

Connect Pronterface to the printer and perform the following:

1. Home the print head `G28`
2. Fetch values for **M666** using `M503`
3. Move the print head to 1mm above the base of a tower, then use the -0.1mm Z movement until the print head pinches a piece of paper. Count the clicks, there should be ten. Any fewer, and adjust the Z-Stop value for that tower positive. Any more, keep counting past ten to get the additional offset, then subtract that from the Z-Stop value for that tower.
4. Set the updated value with the `M666` command.
5. Commit changes to EEPROM with `M500` and repeat test until ten 0.1mm steps get you to zero.

#### Delta Radius adjustment

After the Z-Stop Adjustment is complete, the Z-distance at 0,0 (bed center) is confirmed. If it's too far away, the Delta Radius must be increased. Too close and the Delta Radius must be decreased. This is a trial-and-error process and will take a while.

`M503` will print the current radius value associated with the **M665** parameter.

The `M665` command will set the radius value.
