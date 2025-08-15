# Maslow CNC Calibration Simulation

**ALWAYS follow these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.**

## Working Effectively

This is a web-based calibration simulation tool for Maslow CNC machines. The application uses a genetic algorithm to find optimal corner positions for machine calibration based on measurement data.

### Running and Testing the Application

- **Start HTTP server**: `cd /home/runner/work/Calibration-Simulation/Calibration-Simulation && python3 -m http.server 8000`
- **Open in browser**: Navigate to `http://localhost:8000/index.html`
- **Test basic functionality**: Click "Refine Measurements 1x" button - should show fitness improvement and corner coordinates
- **Test iterative optimization**: Click "1,000x" button - completes in ~10 seconds with progressive fitness improvements
- **Test visualization**: Canvas should display intersecting lines showing calibration convergence
- **Test all buttons**: "10,000x", "End", and "Converge" buttons should all function without errors

### Build and Dependencies

- **No build process required** - This is a standalone HTML file with embedded JavaScript
- **No package.json or dependencies** - Everything runs directly in the browser
- **No compilation or transpilation needed** - Pure HTML5, CSS, and vanilla JavaScript
- **No linting or formatting tools configured** - Manual code review required

### Validation Scenarios

After making any changes to the code, ALWAYS validate by:
1. **Start the HTTP server** and open the application in browser
2. **Click "Refine Measurements 1x"** - Verify fitness value appears and coordinates update
3. **Click "1,000x"** - Verify progressive fitness improvements logged to console
4. **Check canvas visualization** - Lines should be drawn showing calibration pattern
5. **Verify output text area** - Should display current fitness and corner coordinates
6. **Test all interactive elements** - All buttons should respond without JavaScript errors

### Timing Expectations

- **Server startup**: Immediate (< 1 second)
- **Page load**: Immediate (< 1 second) 
- **Single iteration**: Immediate (< 100ms)
- **1,000 iterations**: ~10 seconds - NEVER CANCEL, let complete
- **10,000 iterations**: ~60-90 seconds - NEVER CANCEL, let complete
- **Converge operation**: Variable time depending on data - NEVER CANCEL

## Repository Structure

### Key Files

```
/home/runner/work/Calibration-Simulation/Calibration-Simulation/
├── index.html                           # Main application (889 lines)
├── Old Test Runs - Ignore.md           # Historical test data
├── Washington Frame Post Build Video.txt # Real calibration measurements
├── 10X Calibration Test.txt            # Test calibration data
├── Altspace concrete anchors.txt       # Additional test data
├── Mini show frame.txt                 # Small frame test data
├── Numbers from Roman.txt              # Additional measurements
├── Tension testing.txt                 # Tension test results
├── Washington Frame Post Move.txt      # Movement test data
├── Washington Steel Frame.txt          # Steel frame measurements
├── Washington frame post reconstruction.txt # Reconstruction data
└── Washtington Frame.txt               # Frame measurements
```

### Application Architecture

The `index.html` file contains:
- **HTML structure**: Buttons for different iteration counts and canvas for visualization
- **CSS styling**: Minimal styling with 1px canvas border
- **JavaScript algorithm**: Genetic algorithm for calibration optimization
- **Test data**: Real measurement arrays embedded in code
- **Visualization logic**: HTML5 Canvas drawing functions

### Key Code Sections in index.html

- **Lines 1-30**: HTML structure and button definitions
- **Lines 31-50**: Initial parameters and true corner values (for validation)
- **Lines 50-200**: Core calibration functions (`computeLinesFitness`, `findMaxFitness`)
- **Lines 200-400**: Mathematical utilities and coordinate transformations  
- **Lines 400-600**: Canvas drawing and visualization functions
- **Lines 600-800**: Button event handlers and iteration logic
- **Lines 800-889**: Initial setup and embedded measurement data

## Common Tasks

### Adding New Test Data

1. **Add measurement array** in the format: `{bl:value, br:value, tr:value, tl:value}`
2. **Replace the measurements variable** around line 878: `var measurements = [...]`
3. **Test with single iteration** to verify data loads correctly
4. **Run 1,000x iterations** to verify convergence behavior

### Modifying Algorithm Parameters

1. **Initial guess values** (lines 841-847): Corner position starting points
2. **Genetic algorithm settings**: Look for mutation rates and population parameters
3. **Convergence criteria**: Check fitness thresholds in optimization loops
4. **ALWAYS test changes** with both single iterations and 1,000x runs

### Debugging Calibration Issues

1. **Check browser console** for JavaScript errors during iterations
2. **Verify measurement data format** - must include bl, br, tr, tl values
3. **Test fitness calculations** with single iterations first
4. **Validate canvas drawing** - lines should intersect near optimal points
5. **Check coordinate transformations** - ensure proper scaling and projection

### UI and Visualization Changes

1. **Canvas modifications**: Canvas created at line ~700, size 1000x600 pixels
2. **Button styling**: Minimal CSS in head section (lines 11-16)
3. **Output formatting**: Text area displays fitness and coordinates
4. **Color coding**: Lines use RGB values based on fitness (see color calculation functions)
5. **ALWAYS test visual changes** by running iterations and verifying display

## Validation Checklist

Before completing any work:
- [ ] HTTP server starts successfully on port 8000
- [ ] Application loads without JavaScript errors in browser console
- [ ] "Refine Measurements 1x" produces fitness output and coordinate updates
- [ ] "1,000x" button completes full iteration cycle (~10 seconds)
- [ ] Canvas displays calibration visualization lines
- [ ] Output text area shows current results
- [ ] All buttons remain responsive after operations
- [ ] No runtime errors in browser developer tools

## Important Notes

- **No CI/CD pipeline** - Manual testing required for all changes
- **No automated tests** - Validation must be done through browser interaction
- **Real measurement data** - Test data files contain actual Maslow CNC calibration measurements
- **Single-file application** - All functionality contained in index.html
- **Genetic algorithm optimization** - Converges to find optimal machine corner positions
- **Canvas visualization** - Shows intersection patterns indicating calibration quality