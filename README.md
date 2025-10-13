# Maslow CNC Calibration Simulation

A web-based calibration tool for [Maslow CNC](https://www.maslowcnc.com/) machines that uses a genetic algorithm to find optimal corner positions for machine calibration based on measurement data.

## What is this?

This application helps calibrate Maslow CNC machines by analyzing belt length measurements and calculating the true corner positions of the machine frame. The Maslow CNC uses a unique cable-driven positioning system where four belts extend from corner anchors to move the cutting sled. Accurate corner positions are critical for precise cutting.

### The Problem

When setting up a Maslow CNC, you need to know the exact coordinates of the four corner anchor points where the belts attach. Small errors in these positions can lead to significant cutting inaccuracies. Manually measuring large frames (typically 8-10 feet wide) with millimeter precision is difficult.

### The Solution

This tool uses a **genetic algorithm** to reverse-engineer the corner positions from belt length measurements taken at various known locations on the work surface. By measuring belt lengths at multiple points and applying optimization techniques, the algorithm converges on the true corner positions.

## How It Works

1. **Measurement Phase**: The user moves the cutting sled to various calibration points on the work surface and records the belt lengths (tl, tr, bl, br = top-left, top-right, bottom-left, bottom-right)

2. **Optimization Phase**: The genetic algorithm:
   - Starts with an initial guess for corner positions
   - Simulates belt lines from guessed corners to measurement points
   - Calculates a "fitness" score based on how well the lines converge
   - Iteratively refines corner positions to maximize fitness
   - Converges on optimal corner coordinates

3. **Visualization**: An HTML5 canvas displays the intersecting belt lines, allowing visual verification of calibration quality

## Usage

### Running the Application

This is a standalone HTML application with no build process or dependencies.

1. **Start a local web server**:
   ```bash
   cd /home/runner/work/Calibration-Simulation/Calibration-Simulation
   python3 -m http.server 8000
   ```

2. **Open in browser**:
   Navigate to `http://localhost:8000/index.html`

3. **Load calibration data**:
   - Paste your measurement data in the text area
   - Format: `{bl:value, br:value, tr:value, tl:value},{bl:value, br:value, tr:value, tl:value},...`
   - Click "Parse Calibration Data"

4. **Run optimization**:
   - Click "Refine Measurements 1x" for a single iteration
   - Click "1,000x" for 1000 iterations (~10 seconds)
   - Click "10,000x" for 10,000 iterations (~60-90 seconds)
   - Click "Converge" to automatically iterate until convergence

### Input Format

Measurements should be in the format:
```
{bl:1942.31, br:1952.85, tr:2053.05, tl:2051.76},
{bl:2132.14, br:1584.18, tr:1955.15, tl:2154.52},
{bl:1635.03, br:2732.93, tr:2400.27, tl:996.45}
```

Where:
- `bl` = bottom-left belt length (mm)
- `br` = bottom-right belt length (mm)
- `tr` = top-right belt length (mm)
- `tl` = top-left belt length (mm)

### Output

The application displays:
- **Fitness score**: Lower is better (represents measurement error)
- **Corner coordinates**: The calculated (x, y) positions of each corner
  - Top-left (tl), Top-right (tr)
  - Bottom-left (bl), Bottom-right (br)
- **Visual representation**: Canvas showing belt line intersections

## Repository Structure

```
.
├── index.html                              # Main application (single file)
├── README.md                               # This file
├── .github/
│   └── copilot-instructions.md            # Detailed instructions for AI agents
├── 10X Calibration Test.txt               # Test calibration data
├── 100 Point Calibrations.txt             # Extended test data
├── Washington Frame Post Build Video.txt  # Real calibration measurements
├── Washington Steel Frame.txt             # Steel frame measurements
├── Altspace concrete anchors.txt          # Additional test data
├── Mini show frame.txt                    # Small frame test data
├── Numbers from Roman.txt                 # Additional measurements
├── Tension testing.txt                    # Tension test results
└── [various other test data files]        # Historical measurement data
```

## Technology Stack

- **Pure HTML5**: Single-file application
- **Vanilla JavaScript**: No frameworks or dependencies
- **HTML5 Canvas**: For visualization
- **Genetic Algorithm**: Custom optimization implementation

## Algorithm Details

The calibration uses a variant of the **magnetically attracted lines** fitness function:

1. Four lines are drawn from the guessed corner positions
2. Each line has a length equal to the measured belt length
3. Lines are allowed to rotate (theta adjustment) to find intersection points
4. Fitness is calculated based on how closely the four line endpoints converge
5. The genetic algorithm adjusts corner positions to minimize endpoint dispersion

Key parameters:
- Initial frame dimensions: ~3048mm x 2186mm (configurable)
- Measurement precision: Sub-millimeter convergence possible
- Typical calibration: 7-100 measurement points
- Belt projection compensation: Accounts for non-planar belt geometry

## Test Data Files

The repository includes real-world test data from various Maslow CNC setups:

- **Washington Frame** series: Full-size frame calibrations
- **10X Calibration Test**: High-precision repeated measurements
- **100 Point Calibrations**: Extended calibration grids
- **Mini show frame**: Small demonstration frame

These files contain actual belt measurements and can be used to test algorithm changes.

## Contributing

When making changes to this repository:

1. **Test thoroughly**: Run the application and verify all buttons work
2. **Validate with real data**: Use the included test data files
3. **Check visualization**: Ensure canvas rendering works correctly
4. **No build needed**: Changes to index.html take effect immediately
5. **Browser testing**: Test in modern browsers (Chrome, Firefox, Safari, Edge)

See `.github/copilot-instructions.md` for detailed development guidelines.

## Browser Compatibility

- Modern browsers with HTML5 Canvas support
- JavaScript ES6+ features used
- No Internet Explorer support

## License

[Add license information here]

## Credits

Developed for the Maslow CNC community to improve machine calibration accuracy and ease of setup.

## Additional Resources

- [Maslow CNC Website](https://www.maslowcnc.com/)
- [Maslow Forums](https://forums.maslowcnc.com/)
- Algorithm based on geometric optimization and intersection analysis

## Troubleshooting

**Problem**: Calibration doesn't converge  
**Solution**: Ensure measurements are from diverse locations across the work surface. More measurement points = better accuracy.

**Problem**: Fitness score doesn't improve  
**Solution**: Check that measurement data is formatted correctly. All four belt lengths must be present for each point.

**Problem**: Canvas doesn't display  
**Solution**: Check browser console for JavaScript errors. Ensure you're running via a web server (not file://).

**Problem**: Results seem inaccurate  
**Solution**: Verify belt projection compensation is appropriate for your setup. Z-heights of belt anchors affect the calculations (see `projectMeasurement` function).
