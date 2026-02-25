This web application is an interactive seismic analysis tool designed for SAC (Seismic Analysis Code) waveform data. It enables manual picking of P‑ and S‑wave arrival times, computes hypocentral parameters using multiple methods, and visualises results on a map. Below is a breakdown of its core features and performance.
1. Data Loading & Waveform Display

    SAC file reader – Uses Pyodide (Python in the browser) with a custom SAC header parser to read local .sac files. Extracted metadata includes station code, coordinates (STLA, STLO), sampling interval (DELTA), number of points, and absolute start time.

    Waveform canvas – Displays the normalised trace of the currently selected station.

        Navigation: wheel zoom (centred on mouse), drag pan, double‑click to reset view to the full trace.

        Interactive picking: click on the waveform to set a P‑ or S‑wave arrival (red/green vertical lines, respectively). Picking mode toggles via dedicated buttons.

2. Pick Management & Wadati Diagram

    Pick summary table – Lists all stations with their absolute UTC times for P and S picks.

    Wadati diagram – Plots (S‑P) against absolute P‑wave time for stations with both picks.

        A linear regression yields the slope m, origin time To (intercept on the time axis), and Vp/Vs = m + 1.

        The result updates the global Vp/Vs field and can be used for subsequent depth calculations.

3. Epicentre Determination via Chord Intersection

    Hypocentral distance (Dhyp) – For each station with P and S picks, two equivalent formulas are computed:

        Dhyp = dt * (Vp * Vs) / (Vp - Vs) (classic formula)

        Dhyp = dt / (1/Vs - 1/Vp) (slowness‑based)

        A table compares both values, warning if discrepancies exceed tolerance.

    Map & circles – A Leaflet map displays station locations. After selecting exactly three stations (checkboxes) and clicking “Solve by Chords”:

        Circles are drawn with radius = Dhyp (km) × 1000 meters centred on each station.

        Pairs of circles intersect, generating two points each; connecting these points forms chords.

        The intersection of any two chords gives the epicentre (yellow marker).

    Geodesic epicentral distance – Uses the Haversine formula to compute accurate distances from the epicentre to each station, improving depth precision.

4. Depth Calculation

    Robust depth – For each station, the vertical depth h_i is derived from h_i = √(Dhyp² – Depi²).

    Aggregation via median ± robust standard deviation (based on MAD) provides the final depth estimate, shown together with the epicentre coordinates.

    A separate table lists Dhyp, epicentral distance Depi, and individual h_i for the three selected stations.

5. User Interface & Workflow

    Layout: Two‑column grid.

        Left column: SAC file input, station list, pick summary, Wadati controls, velocity inputs, station checkboxes, solve button, and result tables.

        Right column: waveform canvas (top), Wadati canvas + map (bottom).

    Map layers: Toggle between satellite (Esri) and topography (OpenTopoMap). Coordinates of the mouse cursor are shown in a floating box.

    Reset options: Clear picks for the current station or all stations; clear map layers and results.

6. Technical Highlights

    Pyodide – Enables in‑browser SAC header parsing without a server.

    Canvas rendering – HiDPI‑aware drawing with automatic axis ticks and labels.

    Leaflet integration – Dynamic map updates, circle drawing, and geodesic distance calculations.

    Robust statistics – Depth uncertainty is expressed as median ± 1.4826 × MAD, providing resilience to outliers.

This application offers a self‑contained, educational, and practical environment for seismic event location using manual picks and classic circle‑intersection methods.
