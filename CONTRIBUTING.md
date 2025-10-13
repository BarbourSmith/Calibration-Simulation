# Contributing to Maslow CNC Calibration Simulation

Thank you for your interest in contributing to this project! This guide will help you understand the codebase and make effective contributions.

## Quick Start for Developers

### Prerequisites

- A modern web browser (Chrome, Firefox, Safari, or Edge)
- Python 3 (for running the local web server)
- Git (for version control)
- A text editor or IDE

### Setting Up Your Development Environment

1. **Clone the repository**:
   ```bash
   git clone https://github.com/BarbourSmith/Calibration-Simulation.git
   cd Calibration-Simulation
   ```

2. **Start the development server**:
   ```bash
   python3 -m http.server 8000
   ```

3. **Open in browser**:
   Navigate to `http://localhost:8000/index.html`

4. **Make changes**:
   Edit `index.html` directly - changes take effect on page refresh

That's it! No build process, no dependencies to install.

## Code Structure

### index.html Architecture

The entire application is contained in a single HTML file (~977 lines) with three main sections:

1. **HTML Structure (lines 1-37)**
   - Control buttons (Refine 1x, 1000x, 10000x, End, Converge)
   - Input textarea for calibration data
   - Output textarea for results
   - Canvas container for visualization

2. **JavaScript Core (lines 38-900+)**
   - Mathematical functions
   - Calibration algorithm
   - Fitness calculation
   - Canvas drawing
   - Event handlers

3. **Inline CSS (lines 11-15)**
   - Minimal styling
   - Layout controls

### Key Functions

#### Measurement and Simulation
- `takeSimulatedMeasurement(x, y, randomError, constantError)` - Simulates measurements with error
- `projectMeasurement(measurement)` - Compensates for belt angle (z-axis offset)
- `projectMeasurements(measurements)` - Applies projection to all measurements
- `parseCalibrationData(inputText)` - Parses user input into measurement objects

#### Algorithm Core
- `computeLinesFitness(measurements, lastGuess)` - Main fitness evaluation function
- `magneticallyAttractedLinesFitness(measurement, individual)` - Simulates belt lines and calculates convergence
- `computeEndpointFitness(tlLine, trLine, blLine, brLine)` - Measures how well line endpoints converge
- `findMaxFitness(guess, measurements)` - Iterative optimization routine

#### Geometry and Math
- `distanceBetweenPoints(a, b, c, d)` - Euclidean distance calculation
- `computeLineEndpoint(startX, startY, theta, length)` - Projects line from angle and length
- `calculateTensions(x, y, guess)` - Computes belt tensions at a position
- `calculateBeltVector(...)` - 3D belt angle calculations

#### Visualization
- `drawLines(tlLine, trLine, blLine, brLine, guess)` - Renders belt lines on canvas
- `clearCanvas()` - Clears the visualization
- Canvas created dynamically in the "views" div

#### User Interface
- Button event listeners (compute-button, compute-1k-button, etc.)
- `printResults(guess)` - Displays corner coordinates and fitness

## Making Changes

### General Guidelines

1. **Keep it simple**: This is intentionally a single-file application
2. **No dependencies**: Don't add frameworks, build tools, or npm packages
3. **Preserve functionality**: All existing buttons and features must continue to work
4. **Test with real data**: Use the provided test data files to validate changes
5. **Comment your code**: Especially for complex mathematical operations

### Testing Your Changes

After making changes, **always** test by:

1. **Basic functionality test**:
   ```bash
   # Start server
   python3 -m http.server 8000
   ```
   - Open `http://localhost:8000/index.html`
   - Click "Refine Measurements 1x"
   - Verify output appears and coordinates update

2. **Iterative optimization test**:
   - Click "1,000x" button
   - Wait ~10 seconds (do NOT cancel)
   - Verify fitness value improves
   - Check console for errors

3. **Visualization test**:
   - Verify canvas displays intersecting lines
   - Lines should converge near the center
   - Colors should vary based on fitness

4. **Data parsing test**:
   - Paste test data from any `.txt` file
   - Click "Parse Calibration Data"
   - Verify "✓ Parsed N measurements successfully!" message

5. **All buttons test**:
   - Test "10,000x" (takes ~60-90 seconds)
   - Test "End" button
   - Test "Converge" button
   - Ensure no JavaScript errors in console

### Code Style

This project uses a functional programming style:

- **Function definitions at the top**: All functions defined before they're called
- **Execution code at the bottom**: Main execution logic is at the end of the file
- **Descriptive names**: Use clear, descriptive variable and function names
- **JSDoc comments**: Document function parameters and return values
- **Minimal comments**: Code should be self-documenting where possible

Example:
```javascript
/**
 * Computes the distance between two points.
 * @param {number} a - The x-coordinate of the first point.
 * @param {number} b - The y-coordinate of the first point.
 * @param {number} c - The x-coordinate of the second point.
 * @param {number} d - The y-coordinate of the second point.
 * @returns {number} - The distance between the two points.
 */
function distanceBetweenPoints(a, b, c, d) {
    var dx = c - a;
    var dy = d - b;
    return Math.sqrt(dx * dx + dy * dy);
}
```

### Common Modification Scenarios

#### Adding a New Test Data Set

1. Create a `.txt` file with measurements
2. Format: `{bl:value, br:value, tr:value, tl:value},` (one per line or comma-separated)
3. Include descriptive comments about the frame dimensions
4. Test by pasting into the application

#### Modifying the Algorithm

1. Locate the relevant function (see Key Functions above)
2. Make minimal changes
3. Test with at least 3 different data sets
4. Verify fitness convergence still occurs
5. Check that results are physically reasonable

Example areas to modify:
- **Convergence criteria**: Adjust thresholds in optimization loops
- **Fitness function**: Modify `computeEndpointFitness()` or `magneticallyAttractedLinesFitness()`
- **Initial guess**: Change `initialGuess` object values
- **Projection compensation**: Adjust Z-height values in `projectMeasurement()`

#### Changing the Visualization

1. Canvas operations are in drawing functions (search for "canvas" or "ctx")
2. Canvas size is 1000x600 pixels
3. Lines are color-coded based on fitness
4. Always call `clearCanvas()` before redrawing

Example:
```javascript
function drawLines(tlLine, trLine, blLine, brLine, guess) {
    // Get or create canvas
    var canvas = document.getElementById('myCanvas');
    if (!canvas) {
        // Canvas creation code...
    }
    var ctx = canvas.getContext('2d');
    
    // Drawing code here
}
```

#### Adding UI Elements

1. Add HTML elements in the `<body>` section (before `<script>`)
2. Add event listeners in the JavaScript section (at the bottom, with other listeners)
3. Keep styling minimal and inline
4. Test that new elements don't break existing layout

### Debugging Tips

1. **Use browser developer tools**:
   - Open console (F12 or Cmd+Option+I)
   - Check for JavaScript errors
   - Use `console.log()` liberally

2. **Inspect fitness progression**:
   - Add `console.log(guess.fitness)` to track convergence
   - Verify fitness decreases (lower = better)

3. **Validate measurement data**:
   - Check that all four belt lengths are present
   - Ensure values are reasonable (typically 1000-3000mm)
   - Verify no NaN or undefined values

4. **Test with simulated data**:
   - Uncomment the simulated measurement code (around line 916)
   - This generates perfect measurements with controlled error
   - Useful for validating algorithm changes

### Performance Considerations

- **1,000 iterations**: ~10 seconds (acceptable)
- **10,000 iterations**: ~60-90 seconds (acceptable)
- If optimization is slower, check for inefficient loops or calculations
- Canvas clearing/redrawing can be expensive - minimize redraws

### Version Control

1. **Create a branch** for your changes:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make small, focused commits**:
   ```bash
   git add index.html
   git commit -m "Add feature X: brief description"
   ```

3. **Write descriptive commit messages**:
   - Good: "Improve fitness convergence by adjusting step size"
   - Bad: "Update code"

4. **Test before committing**:
   - Run all tests described above
   - Verify no console errors
   - Check that visualization still works

## Submitting Changes

### Pull Request Process

1. **Ensure all tests pass** (see Testing Your Changes above)
2. **Update documentation** if you've changed functionality
3. **Describe your changes** clearly in the PR description
4. **Include test results**: Mention which data sets you tested with
5. **Add screenshots** if you've changed the UI or visualization

### Pull Request Template

```markdown
## Description
Brief description of what this PR does

## Changes Made
- Change 1
- Change 2

## Testing
- [x] Tested with "Refine Measurements 1x"
- [x] Tested with "1,000x" iterations
- [x] Tested with real data from: [filename]
- [x] Verified canvas visualization
- [x] No console errors

## Screenshots
[If applicable, add screenshots of UI or visualization changes]
```

## Understanding the Algorithm

### Genetic Algorithm Basics

This calibration tool uses a genetic algorithm approach:

1. **Population**: A single "individual" representing corner positions
2. **Fitness**: How well the corner positions explain the measurements
3. **Mutation**: Small adjustments to corner positions
4. **Selection**: Keep changes that improve fitness
5. **Iteration**: Repeat until convergence

### Magnetically Attracted Lines

The fitness function uses a novel "magnetically attracted lines" approach:

- Four lines extend from the guessed corners
- Each line has a fixed length (the measured belt length)
- Lines can rotate to find the best fit
- The four lines should meet at a single point (the measurement location)
- Fitness is calculated based on how closely the endpoints converge

This approach is more robust than traditional trilateration because it:
- Handles measurement noise better
- Accounts for belt angle (3D projection)
- Converges even with imperfect initial guesses

### Belt Projection

Real Maslow CNC belts are not parallel to the work surface - they have a Z-component:

```
Corner (Z-height) → Belt at angle → Sled (Z=0)
```

The `projectMeasurement()` function compensates for this by:
1. Calculating the true 3D belt length
2. Projecting it onto the 2D work surface
3. Adjusting measurements accordingly

This is why the `tlZ`, `trZ`, `blZ`, `brZ` constants exist.

## Getting Help

- **Check existing issues**: Someone may have already reported your problem
- **Ask questions**: Open an issue with the "question" label
- **Review test data**: The `.txt` files show expected measurement formats
- **Check console**: Browser console often reveals the issue

## Code of Conduct

- Be respectful and constructive
- Focus on improving the project
- Help others learn and contribute
- Report issues clearly and completely

## Additional Resources

- [Maslow CNC Forums](https://forums.maslowcnc.com/)
- [Maslow CNC Documentation](https://www.maslowcnc.com/)
- JavaScript Canvas API: [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)
- Genetic Algorithms: [Wikipedia](https://en.wikipedia.org/wiki/Genetic_algorithm)

## Questions?

If you have questions about contributing, please:
1. Check this document thoroughly
2. Review `.github/copilot-instructions.md` for AI-specific guidance
3. Open an issue with your question
4. Tag it with "question" label

Thank you for contributing to the Maslow CNC community!
