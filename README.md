<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>SIT Projectile Motion Simulator - Advanced</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        /* ====== BRAND COLOR THEME ====== */
        :root {
            /* Primary brand colors from logo */
            --deep-teal: #0B2F33;
            --soft-mint: #D9E8E2;
            --accent-orange: #F26A2E;
            
            /* Enhanced button colors */
            --button-deep-violet: #4B0082;  /* Deep violet */
            --button-light-purple: #9370DB; /* Light purple */
            --button-dark-red: #FF4444;     /* Light red for trajectory (CHANGED) */
            
            /* UI Colors */
            --text-primary: #0B2F33;
            --text-secondary: #2C4A4E;
            --text-light: #FFFFFF;
            --border-color: #A3C6C0;
            --shadow-light: rgba(11, 47, 51, 0.1);
            --shadow-medium: rgba(11, 47, 51, 0.2);
            --card-bg: rgba(255, 255, 255, 0.9);
        }

        /* ====== BASE STYLES ====== */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--soft-mint);
            color: var(--text-primary);
            min-height: 100vh;
            overflow-x: hidden;
            padding: 0;
            margin: 0;
        }

        /* ====== MAIN LAYOUT ====== */
        .app-container {
            display: flex;
            flex-direction: column;
            min-height: 100vh;
            width: 100%;
            max-width: 100%;
            position: relative;
        }

        /* ====== HEADER SECTION ====== */
        .header-section {
            background-color: var(--deep-teal);
            color: var(--text-light);
            padding: 8px 20px;
            width: 100%;
            box-shadow: 0 4px 12px var(--shadow-medium);
            z-index: 100;
            position: sticky;
            top: 0;
            border-bottom: 3px solid var(--accent-orange);
        }

        .header-content {
            display: flex;
            flex-direction: column;
            max-width: 1200px;
            margin: 0 auto;
            width: 100%;
            align-items: center;
        }

        .main-heading {
            font-size: 28px; /* Reduced from 42px */
            font-weight: 900;
            letter-spacing: 1px; /* Reduced from 2px */
            margin-bottom: 2px; /* Reduced from 4px */
            text-align: center;
            text-transform: uppercase;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
            line-height: 1.1;
        }

        .sub-heading {
            font-size: 14px; /* Reduced from 16px */
            font-weight: 400;
            opacity: 0.9;
            text-align: center;
            letter-spacing: 2px; /* Reduced from 3px */
            text-transform: uppercase;
            line-height: 1.2;
        }

        /* ====== MAIN CONTENT AREA ====== */
        .main-content {
            display: flex;
            flex-direction: column;
            flex: 1;
            max-width: 1200px;
            margin: 0 auto;
            width: 100%;
            padding: 15px 20px 20px 20px;
        }

        /* ====== CONTROL PANEL ====== */
        .control-panel {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 6px 16px var(--shadow-light);
            border: 1px solid var(--border-color);
        }

        .panel-title {
            font-size: 20px;
            font-weight: 700;
            color: var(--deep-teal);
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
            padding-bottom: 10px;
            border-bottom: 2px solid var(--accent-orange);
        }

        .panel-title i {
            color: var(--accent-orange);
        }

        .controls-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 20px;
        }

        .control-group {
            margin-bottom: 15px;
        }

        .control-label {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 8px;
            font-size: 14px;
            font-weight: 600;
            color: var(--text-secondary);
        }

        .control-label i {
            color: var(--accent-orange);
            font-size: 16px;
        }

        .control-value {
            background-color: var(--soft-mint);
            color: var(--deep-teal);
            padding: 4px 12px;
            border-radius: 20px;
            font-weight: 700;
            font-size: 14px;
            border: 1px solid var(--border-color);
            min-width: 70px;
            text-align: center;
        }

        .slider-container {
            margin-bottom: 8px;
        }

        input[type="range"] {
            width: 100%;
            height: 6px;
            -webkit-appearance: none;
            background: linear-gradient(to right, var(--deep-teal), var(--accent-orange));
            border-radius: 3px;
            outline: none;
        }

        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 22px;
            height: 22px;
            border-radius: 50%;
            background: var(--deep-teal);
            cursor: pointer;
            border: 3px solid var(--accent-orange);
            box-shadow: 0 0 5px var(--shadow-medium);
        }

        input[type="range"]::-moz-range-thumb {
            width: 22px;
            height: 22px;
            border-radius: 50%;
            background: var(--deep-teal);
            cursor: pointer;
            border: 3px solid var(--accent-orange);
            box-shadow: 0 0 5px var(--shadow-medium);
        }

        .input-with-unit {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .number-input {
            flex: 1;
            padding: 10px 12px;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            font-size: 14px;
            font-weight: 600;
            color: var(--text-primary);
            background-color: var(--soft-mint);
            outline: none;
            text-align: center;
        }

        .number-input:focus {
            border-color: var(--accent-orange);
            box-shadow: 0 0 0 2px rgba(242, 106, 46, 0.2);
        }

        .unit-label {
            font-size: 14px;
            font-weight: 600;
            color: var(--text-secondary);
            min-width: 40px;
        }

        /* ====== ADVANCED CONTROLS SECTION ====== */
        .advanced-controls {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 6px 16px var(--shadow-light);
            border: 1px solid var(--border-color);
        }

        .advanced-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }

        .advanced-option {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-bottom: 15px;
        }

        .advanced-option select {
            padding: 8px 12px;
            border-radius: 6px;
            border: 1px solid var(--border-color);
            background-color: var(--soft-mint);
            color: var(--text-primary);
            font-weight: 600;
            flex: 1;
        }

        .advanced-option label {
            font-weight: 600;
            color: var(--text-secondary);
            min-width: 140px;
        }

        .toggle-switch {
            position: relative;
            display: inline-block;
            width: 50px;
            height: 24px;
        }

        .toggle-switch input {
            opacity: 0;
            width: 0;
            height: 0;
        }

        .toggle-slider {
            position: absolute;
            cursor: pointer;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background-color: var(--border-color);
            transition: .4s;
            border-radius: 24px;
        }

        .toggle-slider:before {
            position: absolute;
            content: "";
            height: 16px;
            width: 16px;
            left: 4px;
            bottom: 4px;
            background-color: white;
            transition: .4s;
            border-radius: 50%;
        }

        input:checked + .toggle-slider {
            background-color: var(--accent-orange);
        }

        input:checked + .toggle-slider:before {
            transform: translateX(26px);
        }

        /* ====== 3D SIMULATION AREA ====== */
        .simulation-area {
            background-color: var(--card-bg);
            border-radius: 12px;
            margin-bottom: 20px;
            box-shadow: 0 6px 16px var(--shadow-light);
            border: 1px solid var(--border-color);
            position: relative;
            overflow: hidden;
            min-height: 450px;
        }

        .simulation-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 20px;
            background-color: var(--deep-teal);
            color: var(--text-light);
            border-bottom: 1px solid var(--border-color);
        }

        .simulation-title {
            font-size: 18px;
            font-weight: 600;
        }

        .view-controls {
            display: flex;
            gap: 10px;
        }

        .view-btn {
            background-color: var(--button-light-purple);
            color: white;
            border: none;
            border-radius: 6px;
            padding: 8px 16px;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
            transition: all 0.2s;
        }

        .view-btn:hover {
            background-color: #7B68EE;
            transform: translateY(-2px);
        }

        .view-btn.active {
            background-color: var(--accent-orange);
        }

        .fullscreen-toggle {
            background-color: var(--button-deep-violet);
            color: white;
            border: none;
            border-radius: 6px;
            padding: 8px 16px;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
            transition: all 0.2s;
        }

        .fullscreen-toggle:hover {
            background-color: #3A0069;
            transform: translateY(-2px);
        }

        .canvas-container {
            width: 100%;
            height: 400px;
            position: relative;
        }

        #simulation-canvas {
            width: 100%;
            height: 100%;
            display: block;
        }

        /* ====== LAUNCH CONTROLS (BOTTOM OF 3D AREA) ====== */
        .launch-controls {
            position: absolute;
            bottom: 20px;
            right: 20px;
            display: flex;
            flex-direction: column;
            gap: 12px;
            z-index: 10;
        }

        .launch-button {
            background-color: var(--button-light-purple);
            color: var(--deep-teal);
            border: none;
            border-radius: 8px;
            padding: 12px 24px;
            font-size: 16px;
            font-weight: 700;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            box-shadow: 0 4px 12px rgba(147, 112, 219, 0.3);
            transition: all 0.3s;
            min-width: 180px;
        }

        .launch-button:hover {
            background-color: #7B68EE;
            transform: translateY(-2px);
            box-shadow: 0 6px 16px rgba(147, 112, 219, 0.4);
        }

        .launch-button:active {
            transform: translateY(0);
        }

        .launch-button.red {
            background-color: var(--button-dark-red);
            color: white;
            box-shadow: 0 4px 12px rgba(255, 68, 68, 0.3);
        }

        .launch-button.red:hover {
            background-color: #FF6666;
            box-shadow: 0 6px 16px rgba(255, 68, 68, 0.4);
        }

        /* ====== SIMULATION DATA PANEL ====== */
        .data-panel {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 6px 16px var(--shadow-light);
            border: 1px solid var(--border-color);
        }

        .data-title {
            font-size: 18px;
            font-weight: 700;
            color: var(--deep-teal);
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
            padding-bottom: 10px;
            border-bottom: 2px solid var(--accent-orange);
        }

        .data-title i {
            color: var(--accent-orange);
        }

        .data-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
        }

        .data-card {
            background-color: var(--soft-mint);
            border-radius: 10px;
            padding: 15px;
            border: 1px solid var(--border-color);
            transition: all 0.3s;
        }

        .data-card:hover {
            transform: translateY(-4px);
            box-shadow: 0 8px 16px var(--shadow-light);
        }

        .data-label {
            font-size: 13px;
            color: var(--text-secondary);
            margin-bottom: 8px;
            font-weight: 600;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .data-value {
            font-size: 24px;
            font-weight: 800;
            color: var(--deep-teal);
            line-height: 1;
        }

        .data-unit {
            font-size: 14px;
            color: var(--text-secondary);
            margin-left: 4px;
            font-weight: 600;
        }

        /* ====== VISUALIZATION SECTION ====== */
        .visualization-section {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 6px 16px var(--shadow-light);
            border: 1px solid var(--border-color);
        }

        .visualization-title {
            font-size: 18px;
            font-weight: 700;
            color: var(--deep-teal);
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
            padding-bottom: 10px;
            border-bottom: 2px solid var(--accent-orange);
        }

        .visualization-title i {
            color: var(--accent-orange);
        }

        .chart-container {
            width: 100%;
            height: 300px;
            position: relative;
        }

        /* ====== WIND CONTROLS SECTION ====== */
        .wind-controls {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 6px 16px var(--shadow-light);
            border: 1px solid var(--border-color);
        }

        .wind-title {
            font-size: 18px;
            font-weight: 700;
            color: var(--deep-teal);
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
            padding-bottom: 10px;
            border-bottom: 2px solid var(--accent-orange);
        }

        .wind-title i {
            color: var(--accent-orange);
        }

        .wind-control-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
        }

        .wind-direction-control {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 15px;
        }

        .wind-direction-display {
            width: 120px;
            height: 120px;
            position: relative;
            border-radius: 50%;
            background: linear-gradient(135deg, var(--soft-mint), #e8f4f0);
            border: 3px solid var(--border-color);
            box-shadow: 0 4px 8px var(--shadow-light);
        }

        .wind-direction-arrow {
            position: absolute;
            top: 50%;
            left: 50%;
            width: 40px;
            height: 4px;
            background-color: var(--button-deep-violet);
            transform-origin: left center;
            transform: translate(-50%, -50%) rotate(0deg);
            transition: transform 0.3s ease;
        }

        .wind-direction-arrow::after {
            content: '';
            position: absolute;
            right: -10px;
            top: -8px;
            width: 0;
            height: 0;
            border-left: 10px solid var(--button-deep-violet);
            border-top: 10px solid transparent;
            border-bottom: 10px solid transparent;
        }

        .wind-direction-labels {
            display: flex;
            justify-content: space-between;
            width: 100%;
            margin-top: 10px;
            font-size: 12px;
            font-weight: 600;
            color: var(--text-secondary);
        }

        .wind-speed-control {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .wind-speed-value {
            font-size: 20px;
            font-weight: 700;
            color: var(--deep-teal);
            text-align: center;
        }

        /* ====== PROJECTILE CUSTOMIZATION ====== */
        .projectile-customization {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 6px 16px var(--shadow-light);
            border: 1px solid var(--border-color);
        }

        .customization-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }

        .shape-options {
            display: flex;
            gap: 15px;
            flex-wrap: wrap;
        }

        .shape-btn {
            padding: 10px 15px;
            border: 2px solid var(--border-color);
            background-color: var(--soft-mint);
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 5px;
            min-width: 80px;
        }

        .shape-btn:hover {
            border-color: var(--button-light-purple);
            transform: translateY(-3px);
        }

        .shape-btn.active {
            border-color: var(--accent-orange);
            background-color: rgba(242, 106, 46, 0.1);
        }

        .shape-icon {
            font-size: 24px;
            color: var(--deep-teal);
        }

        .color-options {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }

        .color-btn {
            width: 30px;
            height: 30px;
            border-radius: 50%;
            border: 2px solid var(--border-color);
            cursor: pointer;
            transition: all 0.3s;
        }

        .color-btn:hover {
            transform: scale(1.1);
        }

        .color-btn.active {
            border-color: var(--deep-teal);
            box-shadow: 0 0 0 2px white, 0 0 0 4px var(--deep-teal);
        }

        /* ====== SIMULATION HISTORY ====== */
        .simulation-history {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 6px 16px var(--shadow-light);
            border: 1px solid var(--border-color);
            max-height: 400px;
            overflow-y: auto;
        }

        .history-list {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .history-item {
            background-color: var(--soft-mint);
            padding: 12px;
            border-radius: 8px;
            border: 1px solid var(--border-color);
            cursor: pointer;
            transition: all 0.3s;
        }

        .history-item:hover {
            background-color: rgba(11, 47, 51, 0.1);
            transform: translateX(5px);
        }

        .history-item.active {
            border-color: var(--accent-orange);
            background-color: rgba(242, 106, 46, 0.1);
        }

        .history-stats {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            margin-top: 10px;
            font-size: 12px;
        }

        /* ====== PRESETS AND ACTIONS ====== */
        .actions-panel {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 6px 16px var(--shadow-light);
            border: 1px solid var(--border-color);
        }

        .actions-title {
            font-size: 18px;
            font-weight: 700;
            color: var(--deep-teal);
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
            padding-bottom: 10px;
            border-bottom: 2px solid var(--accent-orange);
        }

        .actions-title i {
            color: var(--accent-orange);
        }

        .presets-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: 12px;
            margin-bottom: 20px;
        }

        .preset-button {
            background-color: var(--soft-mint);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 12px 8px;
            font-size: 14px;
            font-weight: 600;
            color: var(--text-primary);
            cursor: pointer;
            transition: all 0.3s;
            text-align: center;
        }

        .preset-button:hover {
            background-color: var(--deep-teal);
            color: white;
            border-color: var(--deep-teal);
            transform: translateY(-3px);
        }

        .action-buttons {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
            gap: 12px;
        }

        .action-button {
            background-color: var(--button-deep-violet);
            color: white;
            border: none;
            border-radius: 8px;
            padding: 12px 16px;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            transition: all 0.3s;
        }

        .action-button:hover {
            transform: translateY(-3px);
            box-shadow: 0 6px 12px rgba(75, 0, 130, 0.3);
        }

        .action-button.reset {
            background-color: var(--accent-orange);
        }

        .action-button.reset:hover {
            box-shadow: 0 6px 12px rgba(242, 106, 46, 0.3);
        }

        /* ====== PLANETARY SIMULATION SECTION ====== */
        .planetary-section {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 25px;
            margin: 20px 0;
            box-shadow: 0 6px 20px rgba(11, 47, 51, 0.1);
            border: 1px solid var(--border-color);
        }

        .section-title {
            font-size: 24px;
            font-weight: 700;
            color: var(--deep-teal);
            margin-bottom: 25px;
            display: flex;
            align-items: center;
            gap: 12px;
            padding-bottom: 15px;
            border-bottom: 3px solid var(--accent-orange);
        }

        .planet-selector {
            margin-bottom: 30px;
        }

        .planet-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 15px;
        }

        .planet-card {
            background-color: white;
            border-radius: 12px;
            padding: 15px;
            display: flex;
            align-items: center;
            gap: 15px;
            cursor: pointer;
            transition: all 0.3s ease;
            border: 2px solid transparent;
            box-shadow: 0 4px 12px rgba(11, 47, 51, 0.08);
        }

        .planet-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 20px rgba(11, 47, 51, 0.15);
            border-color: var(--button-deep-violet);
        }

        .planet-card.active {
            border-color: var(--accent-orange);
            background-color: rgba(242, 106, 46, 0.05);
            box-shadow: 0 6px 18px rgba(242, 106, 46, 0.15);
        }

        .planet-icon {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 22px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        }

        .planet-info {
            flex: 1;
        }

        .planet-name {
            font-size: 16px;
            font-weight: 700;
            color: var(--deep-teal);
            margin-bottom: 4px;
        }

        .planet-gravity {
            font-size: 14px;
            color: var(--text-secondary);
            font-weight: 600;
        }

        .planetary-comparison {
            background-color: rgba(217, 232, 226, 0.3);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 30px;
            border: 1px solid var(--border-color);
        }

        .comparison-controls {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin-bottom: 25px;
            padding-bottom: 20px;
            border-bottom: 2px solid rgba(163, 198, 192, 0.5);
        }

        .planet-control-group {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .planet-control-label {
            font-size: 14px;
            font-weight: 600;
            color: var(--deep-teal);
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .planet-slider {
            width: 100%;
            height: 6px;
            -webkit-appearance: none;
            background: linear-gradient(to right, var(--deep-teal), var(--accent-orange));
            border-radius: 3px;
            outline: none;
        }

        .planet-slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 20px;
            height: 20px;
            border-radius: 50%;
            background: var(--deep-teal);
            cursor: pointer;
            border: 3px solid white;
            box-shadow: 0 0 8px rgba(11, 47, 51, 0.3);
        }

        .planet-slider-value {
            font-size: 14px;
            font-weight: 600;
            color: var(--accent-orange);
            text-align: center;
            padding: 4px 10px;
            background-color: white;
            border-radius: 20px;
            border: 1px solid var(--border-color);
        }

        .planet-simulate-btn {
            background-color: var(--button-light-purple);
            color: var(--deep-teal);
            border: none;
            border-radius: 10px;
            padding: 14px 20px;
            font-size: 16px;
            font-weight: 700;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            transition: all 0.3s;
            box-shadow: 0 4px 12px rgba(147, 112, 219, 0.3);
            align-self: end;
        }

        .planet-simulate-btn:hover {
            background-color: #7B68EE;
            transform: translateY(-3px);
            box-shadow: 0 6px 16px rgba(147, 112, 219, 0.4);
        }

        .planetary-results {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 25px;
        }

        .planet-results-table {
            background-color: white;
            border-radius: 10px;
            padding: 15px;
            border: 1px solid var(--border-color);
            max-height: 300px;
            overflow-y: auto;
        }

        .planet-data-table {
            width: 100%;
            border-collapse: collapse;
        }

        .planet-data-table th {
            background-color: var(--deep-teal);
            color: white;
            padding: 12px;
            text-align: left;
            font-weight: 600;
            font-size: 14px;
            position: sticky;
            top: 0;
        }

        .planet-data-table td {
            padding: 10px 12px;
            border-bottom: 1px solid var(--border-color);
            font-size: 14px;
            color: var(--text-primary);
        }

        .planet-data-table tr:hover {
            background-color: rgba(217, 232, 226, 0.3);
        }

        .planet-chart-container {
            background-color: white;
            border-radius: 10px;
            padding: 15px;
            border: 1px solid var(--border-color);
            height: 300px;
        }

        .planet-3d-viewer {
            background-color: #0A1F22;
            border-radius: 12px;
            overflow: hidden;
            color: white;
            margin-top: 20px;
        }

        .viewer-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 20px;
            background-color: rgba(0, 0, 0, 0.2);
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }

        .viewer-header h3 {
            font-size: 18px;
            font-weight: 600;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .view-controls {
            display: flex;
            gap: 10px;
        }

        .view-btn {
            background-color: rgba(75, 0, 130, 0.8);
            color: white;
            border: none;
            border-radius: 6px;
            padding: 8px 15px;
            font-size: 14px;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
            transition: all 0.2s;
        }

        .view-btn:hover {
            background-color: var(--button-deep-violet);
        }

        #planet-3d-canvas {
            width: 100%;
            height: 400px;
            background-color: #0A1F22;
        }

        .planet-info-panel {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            padding: 20px;
            background-color: rgba(0, 0, 0, 0.3);
        }

        .current-planet {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .planet-display {
            display: flex;
            align-items: center;
            gap: 20px;
        }

        .planet-sphere {
            width: 80px;
            height: 80px;
            border-radius: 50%;
            background: linear-gradient(135deg, #2E8B57, #3CB371);
            box-shadow: 0 0 30px rgba(46, 139, 87, 0.5);
            position: relative;
            overflow: hidden;
        }

        .planet-sphere::before {
            content: '';
            position: absolute;
            top: 15%;
            left: 30%;
            width: 20px;
            height: 20px;
            background-color: rgba(255, 255, 255, 0.3);
            border-radius: 50%;
        }

        .planet-sphere::after {
            content: '';
            position: absolute;
            top: 40%;
            right: 25%;
            width: 10px;
            height: 10px;
            background-color: rgba(255, 255, 255, 0.2);
            border-radius: 50%;
        }

        .planet-details h4 {
            font-size: 20px;
            font-weight: 700;
            margin-bottom: 10px;
            color: white;
        }

        .planet-stats {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .planet-stat {
            display: flex;
            justify-content: space-between;
            font-size: 14px;
        }

        .planet-stat-label {
            color: rgba(255, 255, 255, 0.7);
        }

        .planet-stat-value {
            color: white;
            font-weight: 600;
        }

        .projectile-trajectory {
            background-color: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            padding: 15px;
        }

        .projectile-trajectory h5 {
            font-size: 16px;
            margin-bottom: 12px;
            color: white;
            font-weight: 600;
        }

        .trajectory-stats {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .trajectory-stat {
            display: flex;
            justify-content: space-between;
            font-size: 14px;
            padding: 5px 0;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }

        .trajectory-stat:last-child {
            border-bottom: none;
        }

        .trajectory-stat span {
            color: rgba(255, 255, 255, 0.7);
        }

        .trajectory-stat strong {
            color: var(--button-light-purple);
            font-size: 15px;
        }

        /* ====== FULLSCREEN MODE ====== */
        .fullscreen-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            background-color: var(--deep-teal);
            z-index: 9999;
            display: none;
            flex-direction: column;
        }

        .fullscreen-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 20px;
            background-color: rgba(0, 0, 0, 0.2);
            color: white;
        }

        .fullscreen-title {
            font-size: 20px;
            font-weight: 700;
        }

        .exit-fullscreen {
            background-color: var(--accent-orange);
            color: white;
            border: none;
            border-radius: 6px;
            padding: 10px 20px;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
            transition: all 0.2s;
        }

        .exit-fullscreen:hover {
            background-color: #E55A1F;
            transform: translateY(-2px);
        }

        .fullscreen-canvas-container {
            flex: 1;
            width: 100%;
            position: relative;
            overflow: hidden;
        }

        #fullscreen-canvas {
            width: 100%;
            height: 100%;
            display: block;
        }

        .fullscreen-controls {
            position: absolute;
            bottom: 30px;
            right: 30px;
            display: flex;
            flex-direction: column;
            gap: 15px;
            z-index: 1000;
        }

        .fullscreen-wind-indicator {
            position: absolute;
            top: 30px;
            right: 30px;
            background-color: rgba(0, 0, 0, 0.7);
            color: white;
            padding: 15px;
            border-radius: 10px;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
            z-index: 1000;
        }

        .wind-indicator-arrow {
            width: 50px;
            height: 50px;
            position: relative;
        }

        .wind-indicator-arrow::before {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            width: 30px;
            height: 4px;
            background-color: white;
            transform-origin: left center;
            transform: translate(-50%, -50%) rotate(0deg);
        }

        .wind-indicator-arrow::after {
            content: '';
            position: absolute;
            right: 25%;
            top: 50%;
            transform: translateY(-50%);
            width: 0;
            height: 0;
            border-left: 10px solid white;
            border-top: 8px solid transparent;
            border-bottom: 8px solid transparent;
        }

        /* ====== ABOUT APPLICATION SECTION ====== */
        .about-application {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 25px;
            margin: 20px 0;
            box-shadow: 0 6px 16px var(--shadow-light);
            border: 1px solid var(--border-color);
            text-align: center;
        }

        .about-title {
            font-size: 20px;
            font-weight: 700;
            color: var(--deep-teal);
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            padding-bottom: 10px;
            border-bottom: 2px solid var(--accent-orange);
        }

        .about-subheading {
            font-size: 18px;
            font-weight: 600;
            color: var(--button-deep-violet);
            margin-bottom: 20px;
            font-style: italic;
        }

        .about-content {
            font-size: 16px;
            color: var(--text-secondary);
            line-height: 1.6;
            margin-bottom: 20px;
        }

        .about-creator {
            font-size: 14px;
            color: var(--deep-teal);
            font-weight: 600;
            margin-top: 20px;
            padding-top: 15px;
            border-top: 1px solid var(--border-color);
        }

        .about-button {
            background-color: var(--button-light-purple);
            color: white;
            border: none;
            border-radius: 8px;
            padding: 12px 24px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            transition: all 0.3s;
            margin-top: 15px;
        }

        .about-button:hover {
            background-color: #7B68EE;
            transform: translateY(-2px);
            box-shadow: 0 6px 12px rgba(147, 112, 219, 0.4);
        }

        /* ====== FOOTER ====== */
        .footer-section {
            background-color: var(--deep-teal);
            color: var(--text-light);
            padding: 20px;
            margin-top: 20px;
            text-align: center;
        }

        .footer-content {
            max-width: 1200px;
            margin: 0 auto;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
        }

        .simulation-status {
            display: flex;
            align-items: center;
            gap: 10px;
            font-size: 14px;
        }

        .status-indicator {
            width: 10px;
            height: 10px;
            border-radius: 50%;
            background-color: #4CAF50;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.5; }
            100% { opacity: 1; }
        }

        .copyright {
            font-size: 13px;
            opacity: 0.8;
        }

        /* ====== RESPONSIVE DESIGN ====== */
        @media (max-width: 768px) {
            .main-content {
                padding: 15px;
            }
            
            .controls-grid {
                grid-template-columns: 1fr;
            }
            
            .data-grid {
                grid-template-columns: repeat(2, 1fr);
            }
            
            .presets-grid {
                grid-template-columns: repeat(2, 1fr);
            }
            
            .action-buttons {
                grid-template-columns: 1fr;
            }
            
            .canvas-container {
                height: 350px;
            }
            
            .launch-controls {
                bottom: 15px;
                right: 15px;
            }
            
            .launch-button {
                min-width: 160px;
                padding: 10px 20px;
                font-size: 15px;
            }
            
            .main-heading {
                font-size: 24px;
            }
            
            .sub-heading {
                font-size: 12px;
                letter-spacing: 1px;
            }
            
            .planetary-results {
                grid-template-columns: 1fr;
            }
            
            .planet-grid {
                grid-template-columns: repeat(2, 1fr);
            }
            
            .planet-info-panel {
                grid-template-columns: 1fr;
            }
            
            .comparison-controls {
                grid-template-columns: 1fr;
            }
            
            .wind-control-grid {
                grid-template-columns: 1fr;
            }
            
            .advanced-grid {
                grid-template-columns: 1fr;
            }
            
            .customization-grid {
                grid-template-columns: 1fr;
            }
            
            .about-title {
                font-size: 18px;
            }
            
            .about-subheading {
                font-size: 16px;
            }
            
            .about-content {
                font-size: 14px;
            }
        }

        @media (max-width: 480px) {
            .data-grid {
                grid-template-columns: 1fr;
            }
            
            .presets-grid {
                grid-template-columns: 1fr;
            }
            
            .canvas-container {
                height: 300px;
            }
            
            .main-heading {
                font-size: 22px;
            }
            
            .sub-heading {
                font-size: 11px;
                letter-spacing: 1px;
            }
            
            .planet-grid {
                grid-template-columns: 1fr;
            }
            
            #planet-3d-canvas {
                height: 300px;
            }
            
            .about-button {
                width: 100%;
            }
        }

        /* ====== ORIENTATION LOCK SUPPORT ====== */
        @media screen and (orientation: landscape) {
            .app-container {
                max-width: 100vw;
            }
            
            .main-content {
                max-width: 100%;
            }
            
            .control-panel, .simulation-area, .data-panel, .visualization-section, .actions-panel, .planetary-section {
                width: 100%;
            }
        }

        /* ====== UTILITY CLASSES ====== */
        .hidden {
            display: none !important;
        }

        .text-center {
            text-align: center;
        }

        .mt-10 {
            margin-top: 10px;
        }

        .mb-10 {
            margin-bottom: 10px;
        }

        /* ====== SCROLLBAR STYLING ====== */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }

        ::-webkit-scrollbar-track {
            background: var(--soft-mint);
            border-radius: 4px;
        }

        ::-webkit-scrollbar-thumb {
            background: var(--deep-teal);
            border-radius: 4px;
        }

        ::-webkit-scrollbar-thumb:hover {
            background: var(--accent-orange);
        }
    </style>
</head>
<body>
    <!-- Main App Container -->
    <div class="app-container">
        <!-- Header Section -->
        <header class="header-section">
            <div class="header-content">
                <h1 class="main-heading">Advanced Projectile Motion Simulator</h1>
                <p class="sub-heading">SIT - Shaggar Institute of Technology</p>
            </div>
        </header>

        <!-- Main Content Area -->
        <main class="main-content">
            <!-- Control Panel -->
            <section class="control-panel">
                <h2 class="panel-title"><i class="fas fa-sliders-h"></i> Launch Parameters</h2>
                <div class="controls-grid">
                    <!-- Velocity Control -->
                    <div class="control-group">
                        <div class="control-label">
                            <span><i class="fas fa-tachometer-alt"></i> Initial Velocity</span>
                            <span class="control-value" id="velocity-value">50 m/s</span>
                        </div>
                        <div class="slider-container">
                            <input type="range" id="velocity-slider" min="10" max="200" value="50" step="1">
                        </div>
                        <div class="input-with-unit">
                            <input type="number" class="number-input" id="velocity-input" min="10" max="200" value="50" step="1">
                            <span class="unit-label">m/s</span>
                        </div>
                    </div>

                    <!-- Angle Control -->
                    <div class="control-group">
                        <div class="control-label">
                            <span><i class="fas fa-angle-up"></i> Launch Angle</span>
                            <span class="control-value" id="angle-value">45°</span>
                        </div>
                        <div class="slider-container">
                            <input type="range" id="angle-slider" min="0" max="90" value="45" step="1">
                        </div>
                        <div class="input-with-unit">
                            <input type="number" class="number-input" id="angle-input" min="0" max="90" value="45" step="1">
                            <span class="unit-label">°</span>
                        </div>
                    </div>

                    <!-- Mass Control -->
                    <div class="control-group">
                        <div class="control-label">
                            <span><i class="fas fa-weight-hanging"></i> Projectile Mass</span>
                            <span class="control-value" id="mass-value">10 kg</span>
                        </div>
                        <div class="slider-container">
                            <input type="range" id="mass-slider" min="1" max="100" value="10" step="1">
                        </div>
                        <div class="input-with-unit">
                            <input type="number" class="number-input" id="mass-input" min="1" max="100" value="10" step="1">
                            <span class="unit-label">kg</span>
                        </div>
                    </div>

                    <!-- Gravity Control -->
                    <div class="control-group">
                        <div class="control-label">
                            <span><i class="fas fa-globe-americas"></i> Gravity</span>
                            <span class="control-value" id="gravity-value">9.81 m/s²</span>
                        </div>
                        <div class="slider-container">
                            <input type="range" id="gravity-slider" min="1.62" max="24.79" value="9.81" step="0.01">
                        </div>
                        <div class="input-with-unit">
                            <input type="number" class="number-input" id="gravity-input" min="1.62" max="24.79" value="9.81" step="0.01">
                            <span class="unit-label">m/s²</span>
                        </div>
                    </div>

                    <!-- Air Resistance Control -->
                    <div class="control-group">
                        <div class="control-label">
                            <span><i class="fas fa-wind"></i> Air Resistance</span>
                            <span class="control-value" id="resistance-value">0.1</span>
                        </div>
                        <div class="slider-container">
                            <input type="range" id="resistance-slider" min="0" max="0.5" value="0.1" step="0.01">
                        </div>
                        <div class="input-with-unit">
                            <input type="number" class="number-input" id="resistance-input" min="0" max="0.5" value="0.1" step="0.01">
                            <span class="unit-label">Coefficient</span>
                        </div>
                    </div>

                    <!-- Height Control -->
                    <div class="control-group">
                        <div class="control-label">
                            <span><i class="fas fa-mountain"></i> Launch Height</span>
                            <span class="control-value" id="height-value">10 m</span>
                        </div>
                        <div class="slider-container">
                            <input type="range" id="height-slider" min="0" max="100" value="10" step="1">
                        </div>
                        <div class="input-with-unit">
                            <input type="number" class="number-input" id="height-input" min="0" max="100" value="10" step="1">
                            <span class="unit-label">m</span>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Advanced Controls -->
            <section class="advanced-controls">
                <h2 class="panel-title"><i class="fas fa-cogs"></i> Advanced Physics Parameters</h2>
                <div class="advanced-grid">
                    <div class="advanced-option">
                        <label><i class="fas fa-drafting-compass"></i> Drag Coefficient:</label>
                        <input type="range" id="drag-coefficient" min="0" max="1" value="0.47" step="0.01">
                        <span class="control-value" id="drag-value">0.47</span>
                    </div>
                    <div class="advanced-option">
                        <label><i class="fas fa-plane"></i> Lift Force:</label>
                        <input type="range" id="lift-force" min="0" max="10" value="0" step="0.1">
                        <span class="control-value" id="lift-value">0 N</span>
                    </div>
                    <div class="advanced-option">
                        <label><i class="fas fa-sync-alt"></i> Spin Rate:</label>
                        <input type="range" id="spin-rate" min="0" max="1000" value="0" step="10">
                        <span class="control-value" id="spin-value">0 rpm</span>
                    </div>
                    <div class="advanced-option">
                        <label><i class="fas fa-thermometer-half"></i> Air Density:</label>
                        <input type="range" id="air-density" min="0.5" max="2" value="1.2" step="0.1">
                        <span class="control-value" id="density-value">1.2 kg/m³</span>
                    </div>
                </div>
            </section>

            <!-- Projectile Customization -->
            <section class="projectile-customization">
                <h2 class="panel-title"><i class="fas fa-shapes"></i> Projectile Customization</h2>
                <div class="customization-grid">
                    <div class="control-group">
                        <label class="control-label"><i class="fas fa-shapes"></i> Projectile Shape</label>
                        <div class="shape-options">
                            <button class="shape-btn active" data-shape="sphere">
                                <i class="fas fa-circle shape-icon"></i>
                                <span>Sphere</span>
                            </button>
                            <button class="shape-btn" data-shape="cube">
                                <i class="fas fa-cube shape-icon"></i>
                                <span>Cube</span>
                            </button>
                            <button class="shape-btn" data-shape="pyramid">
                                <i class="fas fa-gem shape-icon"></i>
                                <span>Pyramid</span>
                            </button>
                            <button class="shape-btn" data-shape="cylinder">
                                <i class="fas fa-dot-circle shape-icon"></i>
                                <span>Cylinder</span>
                            </button>
                        </div>
                    </div>
                    <div class="control-group">
                        <label class="control-label"><i class="fas fa-palette"></i> Projectile Color</label>
                        <div class="color-options">
                            <div class="color-btn active" style="background-color: #9370DB;" data-color="#9370DB"></div>
                            <div class="color-btn" style="background-color: #FF4444;" data-color="#FF4444"></div>
                            <div class="color-btn" style="background-color: #4B0082;" data-color="#4B0082"></div>
                            <div class="color-btn" style="background-color: #F26A2E;" data-color="#F26A2E"></div>
                            <div class="color-btn" style="background-color: #2E8B57;" data-color="#2E8B57"></div>
                            <div class="color-btn" style="background-color: #DAA520;" data-color="#DAA520"></div>
                        </div>
                    </div>
                    <div class="control-group">
                        <label class="control-label"><i class="fas fa-draw-polygon"></i> Trajectory Style</label>
                        <div class="shape-options">
                            <button class="shape-btn active" data-trajectory="line">
                                <i class="fas fa-wave-square shape-icon"></i>
                                <span>Line</span>
                            </button>
                            <button class="shape-btn" data-trajectory="dashed">
                                <i class="fas fa-grip-lines shape-icon"></i>
                                <span>Dashed</span>
                            </button>
                            <button class="shape-btn" data-trajectory="points">
                                <i class="fas fa-braille shape-icon"></i>
                                <span>Points</span>
                            </button>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Wind Controls -->
            <section class="wind-controls">
                <h2 class="wind-title"><i class="fas fa-wind"></i> Wind & Environmental Controls</h2>
                <div class="wind-control-grid">
                    <div class="wind-direction-control">
                        <div class="wind-direction-display">
                            <div class="wind-direction-arrow" id="wind-direction-arrow"></div>
                        </div>
                        <div class="wind-direction-labels">
                            <span>W</span>
                            <span>N</span>
                            <span>E</span>
                            <span>S</span>
                        </div>
                        <input type="range" id="wind-direction-slider" min="0" max="360" value="0" step="1">
                        <div class="control-value" id="wind-direction-value">0° (East)</div>
                    </div>
                    
                    <div class="wind-speed-control">
                        <div class="control-label">
                            <span><i class="fas fa-tachometer-alt"></i> Wind Speed</span>
                            <span class="control-value" id="wind-speed-value">0 m/s</span>
                        </div>
                        <div class="slider-container">
                            <input type="range" id="wind-speed-slider" min="0" max="50" value="0" step="0.1">
                        </div>
                        <div class="input-with-unit">
                            <input type="number" class="number-input" id="wind-speed-input" min="0" max="50" value="0" step="0.1">
                            <span class="unit-label">m/s</span>
                        </div>
                        <div class="wind-speed-value" id="wind-display">No Wind</div>
                    </div>
                    
                    <div class="control-group">
                        <div class="advanced-option">
                            <label><i class="fas fa-cloud-rain"></i> Rain Effect:</label>
                            <label class="toggle-switch">
                                <input type="checkbox" id="rain-effect">
                                <span class="toggle-slider"></span>
                            </label>
                        </div>
                        <div class="advanced-option">
                            <label><i class="fas fa-snowflake"></i> Snow Effect:</label>
                            <label class="toggle-switch">
                                <input type="checkbox" id="snow-effect">
                                <span class="toggle-slider"></span>
                            </label>
                        </div>
                        <div class="advanced-option">
                            <label><i class="fas fa-smog"></i> Fog Effect:</label>
                            <input type="range" id="fog-density" min="0" max="100" value="0" step="1">
                            <span class="control-value" id="fog-value">0%</span>
                        </div>
                    </div>
                </div>
            </section>

            <!-- 3D Simulation Area -->
            <section class="simulation-area">
                <div class="simulation-header">
                    <div class="simulation-title">
                        <i class="fas fa-cube"></i> 3D Projectile Simulation
                    </div>
                    <div class="view-controls">
                        <button class="view-btn active" data-view="perspective">
                            <i class="fas fa-eye"></i> Perspective
                        </button>
                        <button class="view-btn" data-view="top">
                            <i class="fas fa-satellite"></i> Top View
                        </button>
                        <button class="view-btn" data-view="side">
                            <i class="fas fa-arrows-alt-h"></i> Side View
                        </button>
                        <button class="fullscreen-toggle" id="fullscreen-toggle">
                            <i class="fas fa-expand"></i> Fullscreen
                        </button>
                    </div>
                </div>
                <div class="canvas-container">
                    <canvas id="simulation-canvas"></canvas>
                    
                    <!-- Launch Controls at Bottom Right of 3D Area -->
                    <div class="launch-controls">
                        <button class="launch-button red" id="reset-simulation">
                            <i class="fas fa-redo"></i> Reset
                        </button>
                        <button class="launch-button" id="launch-button">
                            <i class="fas fa-rocket"></i> Launch Projectile
                        </button>
                        <button class="launch-button" id="multi-launch">
                            <i class="fas fa-layer-group"></i> Multi Launch
                        </button>
                    </div>
                </div>
            </section>

            <!-- Simulation Data Panel -->
            <section class="data-panel">
                <h2 class="data-title"><i class="fas fa-chart-line"></i> Advanced Flight Data</h2>
                <div class="data-grid">
                    <div class="data-card">
                        <div class="data-label"><i class="fas fa-ruler-vertical"></i> Current Height</div>
                        <div class="data-value" id="current-height">0.0<span class="data-unit"> m</span></div>
                    </div>
                    <div class="data-card">
                        <div class="data-label"><i class="fas fa-ruler-horizontal"></i> Current Distance</div>
                        <div class="data-value" id="current-distance">0.0<span class="data-unit"> m</span></div>
                    </div>
                    <div class="data-card">
                        <div class="data-label"><i class="fas fa-clock"></i> Time of Flight</div>
                        <div class="data-value" id="flight-time">0.0<span class="data-unit"> s</span></div>
                    </div>
                    <div class="data-card">
                        <div class="data-label"><i class="fas fa-chart-area"></i> Peak Height</div>
                        <div class="data-value" id="peak-height">0.0<span class="data-unit"> m</span></div>
                    </div>
                    <div class="data-card">
                        <div class="data-label"><i class="fas fa-bullseye"></i> Max Range</div>
                        <div class="data-value" id="max-range">0.0<span class="data-unit"> m</span></div>
                    </div>
                    <div class="data-card">
                        <div class="data-label"><i class="fas fa-tachometer-alt"></i> Current Velocity</div>
                        <div class="data-value" id="current-velocity">0.0<span class="data-unit"> m/s</span></div>
                    </div>
                    <div class="data-card">
                        <div class="data-label"><i class="fas fa-stopwatch"></i> Time to Peak</div>
                        <div class="data-value" id="time-to-peak">0.0<span class="data-unit"> s</span></div>
                    </div>
                    <div class="data-card">
                        <div class="data-label"><i class="fas fa-bolt"></i> Kinetic Energy</div>
                        <div class="data-value" id="kinetic-energy">0.0<span class="data-unit"> kJ</span></div>
                    </div>
                    <div class="data-card">
                        <div class="data-label"><i class="fas fa-compress-arrows-alt"></i> Air Resistance Force</div>
                        <div class="data-value" id="drag-force">0.0<span class="data-unit"> N</span></div>
                    </div>
                    <div class="data-card">
                        <div class="data-label"><i class="fas fa-weight"></i> Weight Force</div>
                        <div class="data-value" id="weight-force">98.1<span class="data-unit"> N</span></div>
                    </div>
                    <div class="data-card">
                        <div class="data-label"><i class="fas fa-fire"></i> Impact Energy</div>
                        <div class="data-value" id="impact-energy">0.0<span class="data-unit"> kJ</span></div>
                    </div>
                    <div class="data-card">
                        <div class="data-label"><i class="fas fa-chart-pie"></i> Energy Efficiency</div>
                        <div class="data-value" id="energy-efficiency">0.0<span class="data-unit"> %</span></div>
                    </div>
                </div>
            </section>

            <!-- Simulation History -->
            <section class="simulation-history">
                <h2 class="panel-title"><i class="fas fa-history"></i> Simulation History</h2>
                <div class="history-list" id="history-list">
                    <!-- History items will be added here -->
                </div>
            </section>

            <!-- Visualization Section -->
            <section class="visualization-section">
                <h2 class="visualization-title"><i class="fas fa-chart-bar"></i> Advanced Trajectory Analysis</h2>
                <div class="chart-container">
                    <canvas id="trajectory-chart"></canvas>
                </div>
            </section>

            <!-- ====== PLANETARY SIMULATION SECTION ====== -->
            <section class="planetary-section">
                <h2 class="section-title"><i class="fas fa-globe-americas"></i> Planetary Projectile Comparison</h2>
                
                <div class="planet-selector">
                    <div class="planet-grid">
                        <div class="planet-card active" data-planet="earth">
                            <div class="planet-icon" style="background: linear-gradient(135deg, #2E8B57, #3CB371);">
                                <i class="fas fa-globe-americas"></i>
                            </div>
                            <div class="planet-info">
                                <div class="planet-name">Earth</div>
                                <div class="planet-gravity">9.81 m/s²</div>
                            </div>
                        </div>
                        
                        <div class="planet-card" data-planet="moon">
                            <div class="planet-icon" style="background: linear-gradient(135deg, #808080, #A9A9A9);">
                                <i class="fas fa-moon"></i>
                            </div>
                            <div class="planet-info">
                                <div class="planet-name">Moon</div>
                                <div class="planet-gravity">1.62 m/s²</div>
                            </div>
                        </div>
                        
                        <div class="planet-card" data-planet="mars">
                            <div class="planet-icon" style="background: linear-gradient(135deg, #C1440E, #E25822);">
                                <i class="fas fa-globe"></i>
                            </div>
                            <div class="planet-info">
                                <div class="planet-name">Mars</div>
                                <div class="planet-gravity">3.71 m/s²</div>
                            </div>
                        </div>
                        
                        <div class="planet-card" data-planet="venus">
                            <div class="planet-icon" style="background: linear-gradient(135deg, #DAA520, #F0E68C);">
                                <i class="fas fa-venus"></i>
                            </div>
                            <div class="planet-info">
                                <div class="planet-name">Venus</div>
                                <div class="planet-gravity">8.87 m/s²</div>
                            </div>
                        </div>
                        
                        <div class="planet-card" data-planet="jupiter">
                            <div class="planet-icon" style="background: linear-gradient(135deg, #D2691E, #F4A460);">
                                <i class="fas fa-globe"></i>
                            </div>
                            <div class="planet-info">
                                <div class="planet-name">Jupiter</div>
                                <div class="planet-gravity">24.79 m/s²</div>
                            </div>
                        </div>
                        
                        <div class="planet-card" data-planet="custom">
                            <div class="planet-icon" style="background: linear-gradient(135deg, #6A5ACD, #9370DB);">
                                <i class="fas fa-cogs"></i>
                            </div>
                            <div class="planet-info">
                                <div class="planet-name">Custom</div>
                                <div class="planet-gravity">Adjustable</div>
                            </div>
                        </div>
                    </div>
                </div>
                
                <div class="planetary-comparison">
                    <div class="comparison-controls">
                        <div class="planet-control-group">
                            <label class="planet-control-label">
                                <i class="fas fa-tachometer-alt"></i> Velocity
                            </label>
                            <input type="range" class="planet-slider" id="planet-velocity" min="10" max="200" value="50">
                            <div class="planet-slider-value">50 m/s</div>
                        </div>
                        
                        <div class="planet-control-group">
                            <label class="planet-control-label">
                                <i class="fas fa-angle-up"></i> Angle
                            </label>
                            <input type="range" class="planet-slider" id="planet-angle" min="0" max="90" value="45">
                            <div class="planet-slider-value">45°</div>
                        </div>
                        
                        <button class="planet-simulate-btn" id="planet-simulate-btn">
                            <i class="fas fa-rocket"></i> Simulate All Planets
                        </button>
                    </div>
                    
                    <div class="planetary-results">
                        <div class="planet-results-table">
                            <table class="planet-data-table">
                                <thead>
                                    <tr>
                                        <th>Planet</th>
                                        <th>Gravity</th>
                                        <th>Max Height</th>
                                        <th>Max Range</th>
                                        <th>Flight Time</th>
                                        <th>Impact Velocity</th>
                                    </tr>
                                </thead>
                                <tbody id="planet-data-body">
                                    <!-- Data will be populated here -->
                                </tbody>
                            </table>
                        </div>
                        
                        <div class="planet-chart-container">
                            <canvas id="planet-comparison-chart"></canvas>
                        </div>
                    </div>
                </div>
                
                <div class="planet-3d-viewer">
                    <div class="viewer-header">
                        <h3><i class="fas fa-eye"></i> 3D Planetary View</h3>
                        <div class="view-controls">
                            <button class="view-btn" id="rotate-view">
                                <i class="fas fa-sync-alt"></i> Auto Rotate
                            </button>
                            <button class="view-btn" id="zoom-view">
                                <i class="fas fa-search"></i> Zoom
                            </button>
                        </div>
                    </div>
                    <div class="planet-3d-canvas" id="planet-3d-canvas">
                        <!-- 3D Canvas will be rendered here -->
                    </div>
                    <div class="planet-info-panel">
                        <div class="current-planet">
                            <div class="planet-display">
                                <div class="planet-sphere" id="current-planet-sphere"></div>
                                <div class="planet-details">
                                    <h4 id="current-planet-name">Earth</h4>
                                    <div class="planet-stats">
                                        <div class="planet-stat">
                                            <span class="planet-stat-label">Gravity:</span>
                                            <span class="planet-stat-value" id="current-gravity">9.81 m/s²</span>
                                        </div>
                                        <div class="planet-stat">
                                            <span class="planet-stat-label">Diameter:</span>
                                            <span class="planet-stat-value" id="current-diameter">12,742 km</span>
                                        </div>
                                        <div class="planet-stat">
                                            <span class="planet-stat-label">Atmosphere:</span>
                                            <span class="planet-stat-value" id="current-atmosphere">Yes</span>
                                        </div>
                                        <div class="planet-stat">
                                            <span class="planet-stat-label">Escape Velocity:</span>
                                            <span class="planet-stat-value" id="current-escape">11.2 km/s</span>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="projectile-trajectory">
                                <h5>Projectile Trajectory</h5>
                                <div class="trajectory-stats">
                                    <div class="trajectory-stat">
                                        <span>Peak Altitude:</span>
                                        <strong id="trajectory-altitude">127.4 m</strong>
                                    </div>
                                    <div class="trajectory-stat">
                                        <span>Orbital Velocity:</span>
                                        <strong id="trajectory-velocity">7.9 km/s</strong>
                                    </div>
                                    <div class="trajectory-stat">
                                        <span>Impact Force:</span>
                                        <strong id="impact-force">245.3 kN</strong>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </section>

            <!-- ====== ABOUT APPLICATION SECTION ====== -->
            <section class="about-application" id="about-section" style="display: none;">
                <h2 class="about-title"><i class="fas fa-info-circle"></i> About Application</h2>
                <div class="about-subheading">"Integrity First, Excellence Always"</div>
                <div class="about-content">
                    <p>This Advanced Projectile Motion Simulator is a comprehensive physics simulation tool that allows users to explore projectile motion in a realistic 3D environment with advanced physics parameters.</p>
                    <p>The simulator features:</p>
                    <ul style="text-align: left; margin: 15px 0; padding-left: 20px;">
                        <li>Real-time 3D projectile visualization</li>
                        <li>Advanced physics parameters (drag coefficient, lift force, spin rate)</li>
                        <li>Wind and environmental effects</li>
                        <li>Planetary gravity comparisons</li>
                        <li>Real-time data analysis and charts</li>
                        <li>Multiple projectile shapes and customization options</li>
                    </ul>
                </div>
                <div class="about-creator">
                    This application was created by <strong>Bekama Regasa Negewo</strong>,<br>
                    1st year student at Shaggar Institute of Technology, Ethiopia.
                </div>
                <button class="about-button" id="close-about">
                    <i class="fas fa-times"></i> Close
                </button>
            </section>

            <!-- Presets and Actions Section -->
            <section class="actions-panel">
                <h2 class="actions-title"><i class="fas fa-cogs"></i> Advanced Controls</h2>
                
                <div class="presets-grid">
                    <button class="preset-button" data-preset="cannon">Cannon Shot</button>
                    <button class="preset-button" data-preset="mortar">Mortar Fire</button>
                    <button class="preset-button" data-preset="sniper">Sniper Rifle</button>
                    <button class="preset-button" data-preset="space">Space Launch</button>
                    <button class="preset-button" data-preset="maxrange">Max Range</button>
                    <button class="preset-button" data-preset="moon">Moon Gravity</button>
                    <button class="preset-button" data-preset="jupiter">Jupiter Gravity</button>
                    <button class="preset-button" data-preset="parabolic">Perfect Parabola</button>
                </div>
                
                <div class="action-buttons">
                    <button class="action-button" id="randomize-button">
                        <i class="fas fa-random"></i> Randomize
                    </button>
                    <button class="action-button" id="pause-button">
                        <i class="fas fa-pause"></i> Pause
                    </button>
                    <button class="action-button" id="history-replay">
                        <i class="fas fa-play-circle"></i> Replay History
                    </button>
                    <button class="action-button reset" id="reset-all-button">
                        <i class="fas fa-trash-alt"></i> Reset All
                    </button>
                    <button class="action-button" id="export-button">
                        <i class="fas fa-download"></i> Export Data
                    </button>
                    <button class="action-button" id="export-json-button">
                        <i class="fas fa-file-code"></i> Export JSON
                    </button>
                    <button class="action-button" id="reset-defaults">
                        <i class="fas fa-undo"></i> Reset Defaults
                    </button>
                    <button class="action-button" id="save-preset">
                        <i class="fas fa-save"></i> Save Preset
                    </button>
                    <button class="action-button" id="about-button">
                        <i class="fas fa-info-circle"></i> About Application
                    </button>
                </div>
            </section>
        </main>

        <!-- Footer -->
        <footer class="footer-section">
            <div class="footer-content">
                <div class="simulation-status">
                    <div class="status-indicator"></div>
                    <span id="status-message">Advanced Simulation Ready</span>
                </div>
                <div class="copyright">
                    SIT Advanced Projectile Motion Simulator v4.0 | Enhanced 3D Physics with Advanced Features
                </div>
            </div>
        </footer>

        <!-- Fullscreen Mode -->
        <div class="fullscreen-overlay" id="fullscreen-overlay">
            <div class="fullscreen-header">
                <div class="fullscreen-title">Fullscreen 3D Simulation</div>
                <button class="exit-fullscreen" id="exit-fullscreen">
                    <i class="fas fa-compress"></i> Exit Fullscreen
                </button>
            </div>
            <div class="fullscreen-canvas-container">
                <canvas id="fullscreen-canvas"></canvas>
                
                <!-- Fullscreen Wind Indicator -->
                <div class="fullscreen-wind-indicator" id="fullscreen-wind-indicator">
                    <div class="wind-indicator-arrow" id="fullscreen-wind-arrow"></div>
                    <div id="fullscreen-wind-speed">Wind: 0 m/s</div>
                </div>
                
                <!-- Fullscreen Launch Controls -->
                <div class="fullscreen-controls">
                    <button class="launch-button red" id="fullscreen-reset">
                        <i class="fas fa-redo"></i> Reset
                    </button>
                    <button class="launch-button" id="fullscreen-launch">
                        <i class="fas fa-rocket"></i> Launch Projectile
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // ====== GLOBAL VARIABLES ======
        let scene, camera, renderer, controls;
        let fullscreenScene, fullscreenCamera, fullscreenRenderer, fullscreenControls;
        let projectile, trajectoryLine, ground, gridHelper, skybox, mountains = [];
        let simulationRunning = false;
        let simulationPaused = false;
        let fullscreenMode = false;
        let simulationData = [];
        let trajectoryChart;
        let simulationHistory = [];
        let historyIndex = 0;
        let currentProjectileShape = 'sphere';
        let currentProjectileColor = '#9370DB';
        let currentTrajectoryStyle = 'line';
        let currentView = 'perspective';
        let dragCoefficient = 0.47;
        let liftForce = 0;
        let spinRate = 0;
        let airDensity = 1.2;
        let rainEffect = false;
        let snowEffect = false;
        let fogDensity = 0;
        let rainParticles = [];
        let snowParticles = [];
        
        // Enhanced environment variables
        let terrainMeshes = [];
        let sunLight, ambientLight;
        
        // Planetary simulation variables
        let planetaryScene, planetaryCamera, planetaryRenderer, planetaryControls;
        let planetMesh, planetaryTrajectoryLine;
        let currentPlanet = 'earth';
        let planetChart;
        
        // Physics parameters with default values
        let initialVelocity = 50;
        let launchAngle = 45;
        let projectileMass = 10;
        let gravity = 9.81;
        let airResistance = 0.1;
        let launchHeight = 10;
        
        // Wind parameters
        let windEnabled = true;
        let windSpeed = 0;
        let windDirection = 0; // 0 = East, 90 = North, 180 = West, 270 = South
        let windVector = { x: 0, y: 0 };
        
        // Simulation variables
        let simulationTime = 0;
        let currentHeight = 0;
        let currentDistance = 0;
        let currentVelocity = 0;
        let peakHeight = 0;
        let maxRange = 0;
        let flightTime = 0;
        let timeToPeak = 0;
        let kineticEnergy = 0;
        let dragForce = 0;
        let weightForce = 0;
        let impactEnergy = 0;
        let energyEfficiency = 0;
        
        // Animation variables
        let animationId = null;
        let lastTimestamp = 0;
        let simulationStartTime = 0;
        
        // Fullscreen simulation variables
        let fullscreenProjectile, fullscreenTrajectoryLine;
        let fullscreenSimulationRunning = false;
        let fullscreenSimulationPaused = false;
        let fullscreenSimulationTime = 0;
        let fullscreenSimulationStartTime = 0;
        let fullscreenAnimationId = null;
        
        // Planetary data
        const planets = {
            earth: {
                name: 'Earth',
                gravity: 9.81,
                diameter: '12,742 km',
                atmosphere: 'Yes',
                escapeVelocity: '11.2 km/s',
                color: '#2E8B57',
                trajectoryColor: '#3CB371'
            },
            moon: {
                name: 'Moon',
                gravity: 1.62,
                diameter: '3,474 km',
                atmosphere: 'No',
                escapeVelocity: '2.4 km/s',
                color: '#808080',
                trajectoryColor: '#A9A9A9'
            },
            mars: {
                name: 'Mars',
                gravity: 3.71,
                diameter: '6,779 km',
                atmosphere: 'Yes (thin)',
                escapeVelocity: '5.0 km/s',
                color: '#C1440E',
                trajectoryColor: '#E25822'
            },
            venus: {
                name: 'Venus',
                gravity: 8.87,
                diameter: '12,104 km',
                atmosphere: 'Yes (dense)',
                escapeVelocity: '10.4 km/s',
                color: '#DAA520',
                trajectoryColor: '#F0E68C'
            },
            jupiter: {
                name: 'Jupiter',
                gravity: 24.79,
                diameter: '139,820 km',
                atmosphere: 'Yes (gas giant)',
                escapeVelocity: '59.5 km/s',
                color: '#D2691E',
                trajectoryColor: '#F4A460'
            },
            custom: {
                name: 'Custom',
                gravity: 9.81,
                diameter: 'Variable',
                atmosphere: 'Adjustable',
                escapeVelocity: 'Variable',
                color: '#6A5ACD',
                trajectoryColor: '#9370DB'
            }
        };
        
        // ====== DOM ELEMENTS ======
        // Control elements
        const velocitySlider = document.getElementById('velocity-slider');
        const velocityInput = document.getElementById('velocity-input');
        const velocityValue = document.getElementById('velocity-value');
        
        const angleSlider = document.getElementById('angle-slider');
        const angleInput = document.getElementById('angle-input');
        const angleValue = document.getElementById('angle-value');
        
        const massSlider = document.getElementById('mass-slider');
        const massInput = document.getElementById('mass-input');
        const massValue = document.getElementById('mass-value');
        
        const gravitySlider = document.getElementById('gravity-slider');
        const gravityInput = document.getElementById('gravity-input');
        const gravityValue = document.getElementById('gravity-value');
        
        const resistanceSlider = document.getElementById('resistance-slider');
        const resistanceInput = document.getElementById('resistance-input');
        const resistanceValue = document.getElementById('resistance-value');
        
        const heightSlider = document.getElementById('height-slider');
        const heightInput = document.getElementById('height-input');
        const heightValue = document.getElementById('height-value');
        
        // Advanced controls
        const dragCoefficientSlider = document.getElementById('drag-coefficient');
        const dragValue = document.getElementById('drag-value');
        const liftForceSlider = document.getElementById('lift-force');
        const liftValue = document.getElementById('lift-value');
        const spinRateSlider = document.getElementById('spin-rate');
        const spinValue = document.getElementById('spin-value');
        const airDensitySlider = document.getElementById('air-density');
        const densityValue = document.getElementById('density-value');
        
        // Environmental effects
        const rainEffectCheckbox = document.getElementById('rain-effect');
        const snowEffectCheckbox = document.getElementById('snow-effect');
        const fogDensitySlider = document.getElementById('fog-density');
        const fogValue = document.getElementById('fog-value');
        
        // Wind control elements
        const windDirectionSlider = document.getElementById('wind-direction-slider');
        const windDirectionValue = document.getElementById('wind-direction-value');
        const windDirectionArrow = document.getElementById('wind-direction-arrow');
        const windSpeedSlider = document.getElementById('wind-speed-slider');
        const windSpeedInput = document.getElementById('wind-speed-input');
        const windSpeedValue = document.getElementById('wind-speed-value');
        const windDisplay = document.getElementById('wind-display');
        const fullscreenWindIndicator = document.getElementById('fullscreen-wind-indicator');
        const fullscreenWindArrow = document.getElementById('fullscreen-wind-arrow');
        const fullscreenWindSpeed = document.getElementById('fullscreen-wind-speed');
        
        // Button elements
        const launchButton = document.getElementById('launch-button');
        const resetButton = document.getElementById('reset-simulation');
        const multiLaunchButton = document.getElementById('multi-launch');
        const fullscreenToggle = document.getElementById('fullscreen-toggle');
        const exitFullscreenBtn = document.getElementById('exit-fullscreen');
        const randomizeButton = document.getElementById('randomize-button');
        const pauseButton = document.getElementById('pause-button');
        const resetAllButton = document.getElementById('reset-all-button');
        const exportButton = document.getElementById('export-button');
        const exportJsonButton = document.getElementById('export-json-button');
        const resetDefaultsButton = document.getElementById('reset-defaults');
        const savePresetButton = document.getElementById('save-preset');
        const historyReplayButton = document.getElementById('history-replay');
        const fullscreenLaunchBtn = document.getElementById('fullscreen-launch');
        const fullscreenResetBtn = document.getElementById('fullscreen-reset');
        const aboutButton = document.getElementById('about-button');
        const closeAboutButton = document.getElementById('close-about');
        const aboutSection = document.getElementById('about-section');
        
        // Data display elements
        const currentHeightEl = document.getElementById('current-height');
        const currentDistanceEl = document.getElementById('current-distance');
        const flightTimeEl = document.getElementById('flight-time');
        const peakHeightEl = document.getElementById('peak-height');
        const maxRangeEl = document.getElementById('max-range');
        const currentVelocityEl = document.getElementById('current-velocity');
        const timeToPeakEl = document.getElementById('time-to-peak');
        const kineticEnergyEl = document.getElementById('kinetic-energy');
        const dragForceEl = document.getElementById('drag-force');
        const weightForceEl = document.getElementById('weight-force');
        const impactEnergyEl = document.getElementById('impact-energy');
        const energyEfficiencyEl = document.getElementById('energy-efficiency');
        
        // View control buttons
        const viewButtons = document.querySelectorAll('.view-btn[data-view]');
        
        // Projectile customization elements
        const shapeButtons = document.querySelectorAll('.shape-btn[data-shape]');
        const colorButtons = document.querySelectorAll('.color-btn');
        const trajectoryButtons = document.querySelectorAll('.shape-btn[data-trajectory]');
        
        // Preset buttons
        const presetButtons = document.querySelectorAll('.preset-button');
        
        // History elements
        const historyList = document.getElementById('history-list');
        const statusMessage = document.getElementById('status-message');
        
        // Fullscreen overlay
        const fullscreenOverlay = document.getElementById('fullscreen-overlay');
        
        // Planetary elements
        const planetCards = document.querySelectorAll('.planet-card');
        const planetVelocitySlider = document.getElementById('planet-velocity');
        const planetAngleSlider = document.getElementById('planet-angle');
        const planetVelocityValue = planetVelocitySlider.parentElement.querySelector('.planet-slider-value');
        const planetAngleValue = planetAngleSlider.parentElement.querySelector('.planet-slider-value');
        const planetSimulateBtn = document.getElementById('planet-simulate-btn');
        const planetDataBody = document.getElementById('planet-data-body');
        const rotateViewBtn = document.getElementById('rotate-view');
        const zoomViewBtn = document.getElementById('zoom-view');
        
        // ====== THREE.JS INITIALIZATION ======
        function initThreeJS() {
            // Create main scene
            scene = new THREE.Scene();
            updateFogEffect();
            
            // Create main camera
            const canvas = document.getElementById('simulation-canvas');
            const canvasWidth = canvas.parentElement.clientWidth;
            const canvasHeight = canvas.parentElement.clientHeight;
            
            camera = new THREE.PerspectiveCamera(60, canvasWidth / canvasHeight, 0.1, 5000);
            setCameraView(currentView);
            
            // Create main renderer
            renderer = new THREE.WebGLRenderer({ 
                canvas: canvas,
                antialias: true,
                alpha: true
            });
            renderer.setSize(canvasWidth, canvasHeight);
            renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            renderer.shadowMap.enabled = true;
            renderer.shadowMap.type = THREE.PCFSoftShadowMap;
            
            // Add orbit controls
            controls = new THREE.OrbitControls(camera, renderer.domElement);
            controls.enableDamping = true;
            controls.dampingFactor = 0.05;
            controls.minDistance = 50;
            controls.maxDistance = 500;
            
            // Create enhanced environment
            createEnhancedEnvironment();
            
            // Start animation loop
            animate();
        }
        
        function updateFogEffect() {
            if (scene.fog) {
                scene.fog.density = fogDensity / 5000;
            } else {
                scene.fog = new THREE.FogExp2(0x87CEEB, fogDensity / 5000);
            }
        }
        
        function setCameraView(view) {
            currentView = view;
            
            switch(view) {
                case 'perspective':
                    camera = new THREE.PerspectiveCamera(60, camera.aspect, 0.1, 5000);
                    camera.position.set(100, 80, 150);
                    camera.lookAt(0, 0, 0);
                    break;
                case 'top':
                    camera = new THREE.OrthographicCamera(-200, 200, 200, -200, 0.1, 5000);
                    camera.position.set(0, 300, 0);
                    camera.lookAt(0, 0, 0);
                    camera.zoom = 1;
                    camera.updateProjectionMatrix();
                    break;
                case 'side':
                    camera = new THREE.OrthographicCamera(-200, 200, 200, -200, 0.1, 5000);
                    camera.position.set(300, 0, 0);
                    camera.lookAt(0, 0, 0);
                    camera.zoom = 1;
                    camera.updateProjectionMatrix();
                    break;
            }
            
            // Update controls with new camera
            if (controls) {
                controls.dispose();
                controls = new THREE.OrbitControls(camera, renderer.domElement);
                controls.enableDamping = true;
                controls.dampingFactor = 0.05;
                if (view === 'perspective') {
                    controls.minDistance = 50;
                    controls.maxDistance = 500;
                } else {
                    controls.enableZoom = true;
                    controls.enablePan = true;
                }
            }
        }
        
        function createEnhancedEnvironment() {
            // Create sky gradient background
            createSkyGradient();
            
            // Add sun light
            sunLight = new THREE.DirectionalLight(0xFFFFFF, 1.0);
            sunLight.position.set(200, 300, 100);
            sunLight.castShadow = true;
            sunLight.shadow.mapSize.width = 2048;
            sunLight.shadow.mapSize.height = 2048;
            sunLight.shadow.camera.left = -300;
            sunLight.shadow.camera.right = 300;
            sunLight.shadow.camera.top = 300;
            sunLight.shadow.camera.bottom = -300;
            sunLight.shadow.camera.near = 0.5;
            sunLight.shadow.camera.far = 1000;
            scene.add(sunLight);
            
            // Add ambient light
            ambientLight = new THREE.AmbientLight(0x87CEEB, 0.3);
            scene.add(ambientLight);
            
            // Create realistic ground with squares (grid pattern)
            createSquarePatternGround();
            
            // Add distant mountains (keep large objects, remove small ones)
            createDistantMountains();
            
            // Create projectile
            createProjectile();
            
            // Create trajectory line
            createTrajectoryLine();
            
            // Add launch platform
            createLaunchPlatform();
            
            // Add distance markers
            createDistanceMarkers();
            
            // Add wind visualization
            createWindVisualization();
            
            // Initialize environmental effects
            initEnvironmentalEffects();
        }
        
        function createProjectile() {
            // Remove existing projectile
            if (projectile) {
                scene.remove(projectile);
                if (projectile.geometry) projectile.geometry.dispose();
                if (projectile.material) projectile.material.dispose();
            }
            
            let geometry;
            const color = new THREE.Color(currentProjectileColor);
            
            switch(currentProjectileShape) {
                case 'sphere':
                    geometry = new THREE.SphereGeometry(2, 32, 32);
                    break;
                case 'cube':
                    geometry = new THREE.BoxGeometry(3, 3, 3);
                    break;
                case 'pyramid':
                    geometry = new THREE.ConeGeometry(1.5, 3, 4);
                    break;
                case 'cylinder':
                    geometry = new THREE.CylinderGeometry(1.5, 1.5, 4, 16);
                    break;
                default:
                    geometry = new THREE.SphereGeometry(2, 32, 32);
            }
            
            const material = new THREE.MeshPhongMaterial({ 
                color: color,
                shininess: 100,
                specular: 0xFFFFFF
            });
            
            projectile = new THREE.Mesh(geometry, material);
            projectile.castShadow = true;
            projectile.position.set(0, launchHeight, 0);
            scene.add(projectile);
        }
        
        function createTrajectoryLine() {
            // Remove existing trajectory line
            if (trajectoryLine) {
                scene.remove(trajectoryLine);
                if (trajectoryLine.geometry) trajectoryLine.geometry.dispose();
                if (trajectoryLine.material) trajectoryLine.material.dispose();
            }
            
            let material;
            const color = new THREE.Color(0xFF4444); // Light Red for trajectory
            
            switch(currentTrajectoryStyle) {
                case 'line':
                    material = new THREE.LineBasicMaterial({ 
                        color: color,
                        linewidth: 3,
                        transparent: true,
                        opacity: 0.8
                    });
                    break;
                case 'dashed':
                    material = new THREE.LineDashedMaterial({ 
                        color: color,
                        linewidth: 2,
                        dashSize: 3,
                        gapSize: 2,
                        transparent: true,
                        opacity: 0.8
                    });
                    break;
                case 'points':
                    material = new THREE.PointsMaterial({ 
                        color: color,
                        size: 3,
                        transparent: true,
                        opacity: 0.8
                    });
                    break;
                default:
                    material = new THREE.LineBasicMaterial({ 
                        color: color,
                        linewidth: 3,
                        transparent: true,
                        opacity: 0.8
                    });
            }
            
            const geometry = new THREE.BufferGeometry();
            trajectoryLine = currentTrajectoryStyle === 'points' ? 
                new THREE.Points(geometry, material) : 
                new THREE.Line(geometry, material);
            scene.add(trajectoryLine);
        }
        
        function createSquarePatternGround() {
            // Create a grid of squares
            const squareSize = 50;
            const halfSize = 1000;
            const squaresPerSide = halfSize * 2 / squareSize;
            
            // Alternate colors for checkerboard pattern
            const color1 = 0x0B2F33; // Deep Teal
            const color2 = 0x14393F; // Slightly lighter teal
            
            for (let i = -halfSize; i < halfSize; i += squareSize) {
                for (let j = -halfSize; j < halfSize; j += squareSize) {
                    const isEven = ((i + j) / squareSize) % 2 === 0;
                    const squareColor = isEven ? color1 : color2;
                    
                    const squareGeometry = new THREE.PlaneGeometry(squareSize, squareSize);
                    const squareMaterial = new THREE.MeshLambertMaterial({ 
                        color: squareColor,
                        side: THREE.DoubleSide
                    });
                    
                    const square = new THREE.Mesh(squareGeometry, squareMaterial);
                    square.rotation.x = Math.PI / 2;
                    square.position.set(i + squareSize/2, 0.01, j + squareSize/2);
                    square.receiveShadow = true;
                    
                    scene.add(square);
                }
            }
            
            // Add detailed grid helper on top
            gridHelper = new THREE.GridHelper(1000, 50, 0xA3C6C0, 0xA3C6C0);
            gridHelper.position.y = 0.1;
            scene.add(gridHelper);
        }
        
        function createSkyGradient() {
            // Create a sky gradient using a large sphere
            const skyGeometry = new THREE.SphereGeometry(2000, 32, 32);
            const vertexShader = `
                varying vec3 vWorldPosition;
                void main() {
                    vec4 worldPosition = modelMatrix * vec4(position, 1.0);
                    vWorldPosition = worldPosition.xyz;
                    gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
                }
            `;
            
            const fragmentShader = `
                varying vec3 vWorldPosition;
                uniform vec3 topColor;
                uniform vec3 bottomColor;
                uniform float offset;
                uniform float exponent;
                
                void main() {
                    float h = normalize(vWorldPosition + offset).y;
                    gl_FragColor = vec4(mix(bottomColor, topColor, max(pow(max(h, 0.0), exponent), 0.0)), 1.0);
                }
            `;
            
            const skyMaterial = new THREE.ShaderMaterial({
                vertexShader: vertexShader,
                fragmentShader: fragmentShader,
                uniforms: {
                    topColor: { value: new THREE.Color(0x87CEEB) },
                    bottomColor: { value: new THREE.Color(0xE0F7FF) },
                    offset: { value: 33 },
                    exponent: { value: 0.6 }
                },
                side: THREE.BackSide
            });
            
            skybox = new THREE.Mesh(skyGeometry, skyMaterial);
            scene.add(skybox);
        }
        
        function createDistantMountains() {
            // Create only large, distant mountain ranges
            const mountainColors = [
                0x3A5F40, // Dark green
                0x556B2F, // Olive
                0x696969, // Dim gray
            ];
            
            for (let i = 0; i < 6; i++) {
                const distance = 800 + Math.random() * 600;
                const angle = (i / 6) * Math.PI * 2;
                
                const x = Math.cos(angle) * distance;
                const z = Math.sin(angle) * distance;
                
                // Create large mountains
                const height = 100 + Math.random() * 200;
                const width = 120 + Math.random() * 180;
                const depth = 120 + Math.random() * 180;
                
                const mountainGeometry = new THREE.ConeGeometry(width / 2, height, 8);
                const mountainMaterial = new THREE.MeshLambertMaterial({ 
                    color: mountainColors[Math.floor(Math.random() * mountainColors.length)],
                    flatShading: true
                });
                
                const mountain = new THREE.Mesh(mountainGeometry, mountainMaterial);
                mountain.position.set(x, height / 2, z);
                mountain.castShadow = true;
                mountain.receiveShadow = true;
                
                scene.add(mountain);
                mountains.push(mountain);
            }
        }
        
        function initEnvironmentalEffects() {
            // Initialize rain particles
            if (rainEffect) {
                createRainEffect();
            }
            
            // Initialize snow particles
            if (snowEffect) {
                createSnowEffect();
            }
        }
        
        function createRainEffect() {
            const rainCount = 1000;
            const rainGeometry = new THREE.BufferGeometry();
            const positions = new Float32Array(rainCount * 3);
            
            for (let i = 0; i < rainCount * 3; i += 3) {
                positions[i] = (Math.random() - 0.5) * 1000;
                positions[i + 1] = Math.random() * 500;
                positions[i + 2] = (Math.random() - 0.5) * 1000;
            }
            
            rainGeometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
            
            const rainMaterial = new THREE.PointsMaterial({
                color: 0x8888FF,
                size: 0.5,
                transparent: true,
                opacity: 0.6
            });
            
            const rain = new THREE.Points(rainGeometry, rainMaterial);
            rain.userData = { type: 'rain', speed: 20 };
            scene.add(rain);
            rainParticles.push(rain);
        }
        
        function createSnowEffect() {
            const snowCount = 500;
            const snowGeometry = new THREE.BufferGeometry();
            const positions = new Float32Array(snowCount * 3);
            
            for (let i = 0; i < snowCount * 3; i += 3) {
                positions[i] = (Math.random() - 0.5) * 800;
                positions[i + 1] = Math.random() * 400;
                positions[i + 2] = (Math.random() - 0.5) * 800;
            }
            
            snowGeometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
            
            const snowMaterial = new THREE.PointsMaterial({
                color: 0xFFFFFF,
                size: 1,
                transparent: true,
                opacity: 0.8
            });
            
            const snow = new THREE.Points(snowGeometry, snowMaterial);
            snow.userData = { type: 'snow', speed: 5 };
            scene.add(snow);
            snowParticles.push(snow);
        }
        
        function updateEnvironmentalEffects() {
            // Update rain particles
            rainParticles.forEach(rain => {
                const positions = rain.geometry.attributes.position.array;
                for (let i = 1; i < positions.length; i += 3) {
                    positions[i] -= rain.userData.speed * 0.016;
                    if (positions[i] < -100) {
                        positions[i] = 500;
                        positions[i-1] = (Math.random() - 0.5) * 1000;
                        positions[i+1] = (Math.random() - 0.5) * 1000;
                    }
                }
                rain.geometry.attributes.position.needsUpdate = true;
            });
            
            // Update snow particles
            snowParticles.forEach(snow => {
                const positions = snow.geometry.attributes.position.array;
                for (let i = 0; i < positions.length; i += 3) {
                    positions[i] += (Math.random() - 0.5) * 2;
                    positions[i + 1] -= snow.userData.speed * 0.016;
                    positions[i + 2] += (Math.random() - 0.5) * 2;
                    
                    if (positions[i + 1] < -100) {
                        positions[i + 1] = 400;
                        positions[i] = (Math.random() - 0.5) * 800;
                        positions[i + 2] = (Math.random() - 0.5) * 800;
                    }
                }
                snow.geometry.attributes.position.needsUpdate = true;
            });
        }
        
        function createWindVisualization() {
            // Create wind visualization arrows on the ground
            for (let i = -400; i <= 400; i += 100) {
                for (let j = -400; j <= 400; j += 100) {
                    if (Math.abs(i) < 50 && Math.abs(j) < 50) continue; // Skip near center
                    
                    const arrowGroup = new THREE.Group();
                    
                    // Arrow shaft
                    const shaftGeometry = new THREE.CylinderGeometry(0.1, 0.1, 10, 6);
                    const shaftMaterial = new THREE.MeshBasicMaterial({ color: 0x4B0082 });
                    const shaft = new THREE.Mesh(shaftGeometry, shaftMaterial);
                    shaft.rotation.z = Math.PI / 2;
                    arrowGroup.add(shaft);
                    
                    // Arrow head
                    const headGeometry = new THREE.ConeGeometry(0.3, 1, 6);
                    const headMaterial = new THREE.MeshBasicMaterial({ color: 0x4B0082 });
                    const head = new THREE.Mesh(headGeometry, headMaterial);
                    head.position.x = 5;
                    head.rotation.z = Math.PI / 2;
                    arrowGroup.add(head);
                    
                    arrowGroup.position.set(i, 2, j);
                    arrowGroup.rotation.y = Math.PI;
                    arrowGroup.visible = windEnabled && windSpeed > 0;
                    
                    arrowGroup.userData = { isWindArrow: true };
                    scene.add(arrowGroup);
                }
            }
        }
        
        function updateWindVisualization() {
            scene.traverse(function(object) {
                if (object.userData && object.userData.isWindArrow) {
                    object.visible = windEnabled && windSpeed > 0;
                    if (windEnabled && windSpeed > 0) {
                        const rotationAngle = (windDirection - 90) * Math.PI / 180;
                        object.rotation.y = rotationAngle;
                    }
                }
            });
            
            // Update wind direction arrow in UI
            const rotationAngle = windDirection * Math.PI / 180;
            windDirectionArrow.style.transform = `translate(-50%, -50%) rotate(${rotationAngle}rad)`;
            
            // Update fullscreen wind indicator
            if (fullscreenMode) {
                fullscreenWindArrow.style.transform = `translate(-50%, -50%) rotate(${rotationAngle}rad)`;
                fullscreenWindSpeed.textContent = `Wind: ${windSpeed.toFixed(1)} m/s`;
                fullscreenWindIndicator.style.display = windEnabled && windSpeed > 0 ? 'flex' : 'none';
            }
        }
        
        function createLaunchPlatform() {
            const platformGroup = new THREE.Group();
            
            // Base
            const baseGeometry = new THREE.CylinderGeometry(8, 10, 2, 16);
            const baseMaterial = new THREE.MeshLambertMaterial({ color: 0x2C4A4E });
            const base = new THREE.Mesh(baseGeometry, baseMaterial);
            base.position.y = 1;
            platformGroup.add(base);
            
            // Launcher tube
            const tubeGeometry = new THREE.CylinderGeometry(2, 2.5, launchHeight, 8);
            const tubeMaterial = new THREE.MeshLambertMaterial({ color: 0x555555 });
            const tube = new THREE.Mesh(tubeGeometry, tubeMaterial);
            tube.position.y = launchHeight / 2 + 1;
            platformGroup.add(tube);
            
            platformGroup.position.set(0, 0, 0);
            scene.add(platformGroup);
        }
        
        function createDistanceMarkers() {
            // Create markers at 100m intervals
            for (let i = 100; i <= 1000; i += 100) {
                const markerGeometry = new THREE.CylinderGeometry(0.5, 0.5, 5, 8);
                const markerMaterial = new THREE.MeshLambertMaterial({ color: 0xA3C6C0 });
                const marker = new THREE.Mesh(markerGeometry, markerMaterial);
                marker.position.set(i, 2.5, 0);
                scene.add(marker);
            }
        }
        
        // ====== PLANETARY 3D VIEWER INITIALIZATION ======
        function initPlanetaryViewer() {
            const canvasContainer = document.getElementById('planet-3d-canvas');
            
            // Create planetary scene
            planetaryScene = new THREE.Scene();
            planetaryScene.background = new THREE.Color(0x0A1F22);
            
            // Create planetary camera
            planetaryCamera = new THREE.PerspectiveCamera(60, canvasContainer.clientWidth / 400, 0.1, 1000);
            planetaryCamera.position.set(0, 0, 15);
            
            // Create planetary renderer
            planetaryRenderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
            planetaryRenderer.setSize(canvasContainer.clientWidth, 400);
            planetaryRenderer.setPixelRatio(window.devicePixelRatio);
            canvasContainer.appendChild(planetaryRenderer.domElement);
            
            // Add controls
            planetaryControls = new THREE.OrbitControls(planetaryCamera, planetaryRenderer.domElement);
            planetaryControls.enableDamping = true;
            planetaryControls.dampingFactor = 0.05;
            planetaryControls.rotateSpeed = 0.5;
            planetaryControls.minDistance = 5;
            planetaryControls.maxDistance = 50;
            
            // Add lighting
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
            planetaryScene.add(ambientLight);
            
            const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
            directionalLight.position.set(10, 10, 5);
            planetaryScene.add(directionalLight);
            
            // Create starfield
            createPlanetaryStarfield();
            
            // Create initial planet
            createPlanetaryPlanet('earth');
            
            // Create trajectory
            createPlanetaryTrajectory();
            
            // Handle window resize for planetary viewer
            window.addEventListener('resize', onPlanetaryResize);
            
            // Start animation
            animatePlanetary();
        }
        
        function createPlanetaryStarfield() {
            const starGeometry = new THREE.BufferGeometry();
            const starCount = 1000;
            const positions = new Float32Array(starCount * 3);
            
            for (let i = 0; i < starCount * 3; i += 3) {
                positions[i] = (Math.random() - 0.5) * 100;
                positions[i + 1] = (Math.random() - 0.5) * 100;
                positions[i + 2] = (Math.random() - 0.5) * 100;
            }
            
            starGeometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
            
            const starMaterial = new THREE.PointsMaterial({
                color: 0xffffff,
                size: 0.1,
                sizeAttenuation: true
            });
            
            const stars = new THREE.Points(starGeometry, starMaterial);
            planetaryScene.add(stars);
        }
        
        function createPlanetaryPlanet(planetName) {
            // Remove existing planet
            if (planetMesh) {
                planetaryScene.remove(planetMesh);
                planetMesh.geometry.dispose();
                planetMesh.material.dispose();
            }
            
            const planet = planets[planetName];
            const radius = 3;
            
            // Create planet geometry
            const geometry = new THREE.SphereGeometry(radius, 64, 64);
            
            // Create material
            const material = new THREE.MeshPhongMaterial({
                color: planet.color,
                shininess: 30,
                specular: 0x222222,
                emissive: planet.color,
                emissiveIntensity: 0.1
            });
            
            planetMesh = new THREE.Mesh(geometry, material);
            planetaryScene.add(planetMesh);
            
            // Update planet info display
            updatePlanetInfo(planet);
        }
        
        function createPlanetaryTrajectory() {
            // Create trajectory line
            const points = [];
            const segments = 100;
            
            for (let i = 0; i <= segments; i++) {
                const angle = (i / segments) * Math.PI * 2;
                const x = Math.cos(angle) * 5;
                const y = Math.sin(angle) * 5 * 0.3;
                const z = 0;
                points.push(new THREE.Vector3(x, y, z));
            }
            
            const geometry = new THREE.BufferGeometry().setFromPoints(points);
            const material = new THREE.LineBasicMaterial({
                color: 0x9370DB,
                linewidth: 2,
                transparent: true,
                opacity: 0.7
            });
            
            planetaryTrajectoryLine = new THREE.Line(geometry, material);
            planetaryScene.add(planetaryTrajectoryLine);
        }
        
        function updatePlanetInfo(planet) {
            document.getElementById('current-planet-name').textContent = planet.name;
            document.getElementById('current-gravity').textContent = planet.gravity + ' m/s²';
            document.getElementById('current-diameter').textContent = planet.diameter;
            document.getElementById('current-atmosphere').textContent = planet.atmosphere;
            document.getElementById('current-escape').textContent = planet.escapeVelocity;
            
            // Update planet sphere color
            const planetSphere = document.getElementById('current-planet-sphere');
            planetSphere.style.background = `linear-gradient(135deg, ${planet.color}, ${planet.trajectoryColor})`;
            planetSphere.style.boxShadow = `0 0 30px ${planet.color}80`;
            
            // Calculate trajectory stats
            const velocity = parseInt(planetVelocitySlider.value);
            const angle = parseInt(planetAngleSlider.value);
            updatePlanetaryTrajectoryStats(planet, velocity, angle);
        }
        
        function updatePlanetaryTrajectoryStats(planet, velocity, angle) {
            const angleRad = angle * Math.PI / 180;
            
            // Calculate projectile motion values
            const peakHeight = (velocity * velocity * Math.sin(angleRad) * Math.sin(angleRad)) / (2 * planet.gravity);
            const maxRange = (velocity * velocity * Math.sin(2 * angleRad)) / planet.gravity;
            const flightTime = (2 * velocity * Math.sin(angleRad)) / planet.gravity;
            const impactVelocity = Math.sqrt(velocity * velocity + 2 * planet.gravity * peakHeight);
            const impactForce = projectileMass * impactVelocity / 0.1; // Simplified impact force calculation
            
            // Update display
            document.getElementById('trajectory-altitude').textContent = peakHeight.toFixed(1) + ' m';
            document.getElementById('trajectory-velocity').textContent = velocity + ' m/s';
            document.getElementById('impact-force').textContent = (impactForce / 1000).toFixed(1) + ' kN';
        }
        
        function calculateAllPlanets() {
            const velocity = parseInt(planetVelocitySlider.value);
            const angle = parseInt(planetAngleSlider.value);
            const angleRad = angle * Math.PI / 180;
            
            planetDataBody.innerHTML = '';
            
            Object.values(planets).forEach(planet => {
                // Calculate physics
                const peakHeight = (velocity * velocity * Math.sin(angleRad) * Math.sin(angleRad)) / (2 * planet.gravity);
                const maxRange = (velocity * velocity * Math.sin(2 * angleRad)) / planet.gravity;
                const flightTime = (2 * velocity * Math.sin(angleRad)) / planet.gravity;
                const impactVelocity = Math.sqrt(velocity * velocity + 2 * planet.gravity * peakHeight);
                
                // Create table row
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td><strong>${planet.name}</strong></td>
                    <td>${planet.gravity} m/s²</td>
                    <td>${peakHeight.toFixed(1)} m</td>
                    <td>${maxRange.toFixed(1)} m</td>
                    <td>${flightTime.toFixed(2)} s</td>
                    <td>${impactVelocity.toFixed(1)} m/s</td>
                `;
                planetDataBody.appendChild(row);
            });
            
            // Update chart
            updatePlanetaryComparisonChart(velocity, angle);
        }
        
        function updatePlanetaryComparisonChart(velocity, angle) {
            const ctx = document.getElementById('planet-comparison-chart').getContext('2d');
            
            // Destroy existing chart if it exists
            if (planetChart) {
                planetChart.destroy();
            }
            
            const planetNames = Object.values(planets).map(p => p.name);
            const gravities = Object.values(planets).map(p => p.gravity);
            const angleRad = angle * Math.PI / 180;
            
            const maxRanges = gravities.map(g => 
                (velocity * velocity * Math.sin(2 * angleRad)) / g
            );
            
            planetChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: planetNames,
                    datasets: [{
                        label: 'Max Range (m)',
                        data: maxRanges,
                        backgroundColor: [
                            '#2E8B57', '#808080', '#C1440E', 
                            '#DAA520', '#D2691E', '#6A5ACD'
                        ],
                        borderColor: 'rgba(255, 255, 255, 0.2)',
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            display: true,
                            position: 'top',
                            labels: {
                                color: '#0B2F33',
                                font: {
                                    size: 12,
                                    weight: 'bold'
                                }
                            }
                        },
                        tooltip: {
                            backgroundColor: 'rgba(11, 47, 51, 0.9)',
                            titleColor: '#fff',
                            bodyColor: '#fff',
                            callbacks: {
                                label: function(context) {
                                    return `Range: ${context.raw.toFixed(1)} m`;
                                }
                            }
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            title: {
                                display: true,
                                text: 'Maximum Range (meters)',
                                color: '#0B2F33',
                                font: {
                                    weight: 'bold'
                                }
                            },
                            ticks: {
                                color: '#0B2F33'
                            },
                            grid: {
                                color: 'rgba(11, 47, 51, 0.1)'
                            }
                        },
                        x: {
                            ticks: {
                                color: '#0B2F33'
                            },
                            grid: {
                                display: false
                            }
                        }
                    }
                }
            });
        }
        
        function onPlanetaryResize() {
            const canvasContainer = document.getElementById('planet-3d-canvas');
            planetaryCamera.aspect = canvasContainer.clientWidth / 400;
            planetaryCamera.updateProjectionMatrix();
            planetaryRenderer.setSize(canvasContainer.clientWidth, 400);
        }
        
        function animatePlanetary() {
            requestAnimationFrame(animatePlanetary);
            
            // Rotate planet slowly
            if (planetMesh) {
                planetMesh.rotation.y += 0.005;
            }
            
            // Rotate trajectory line
            if (planetaryTrajectoryLine) {
                planetaryTrajectoryLine.rotation.y += 0.002;
            }
            
            planetaryControls.update();
            planetaryRenderer.render(planetaryScene, planetaryCamera);
        }
        
        // ====== FULLSCREEN MODE INITIALIZATION ======
        function initFullscreenScene() {
            // Create fullscreen scene
            fullscreenScene = new THREE.Scene();
            
            // Create fullscreen camera
            const canvas = document.getElementById('fullscreen-canvas');
            fullscreenCamera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 10000);
            fullscreenCamera.position.set(200, 150, 300);
            
            // Create fullscreen renderer
            fullscreenRenderer = new THREE.WebGLRenderer({ 
                canvas: canvas,
                antialias: true
            });
            fullscreenRenderer.setSize(window.innerWidth, window.innerHeight);
            fullscreenRenderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            fullscreenRenderer.shadowMap.enabled = true;
            fullscreenRenderer.shadowMap.type = THREE.PCFSoftShadowMap;
            
            // Add orbit controls for fullscreen
            fullscreenControls = new THREE.OrbitControls(fullscreenCamera, fullscreenRenderer.domElement);
            fullscreenControls.enableDamping = true;
            fullscreenControls.dampingFactor = 0.05;
            fullscreenControls.minDistance = 50;
            fullscreenControls.maxDistance = 2000;
            
            // Create enhanced fullscreen environment
            createFullscreenEnhancedEnvironment();
            
            // Animate fullscreen scene
            animateFullscreen();
        }
        
        function createFullscreenEnhancedEnvironment() {
            // Add lighting to fullscreen scene
            const fullscreenAmbientLight = new THREE.AmbientLight(0x87CEEB, 0.3);
            fullscreenScene.add(fullscreenAmbientLight);
            
            const fullscreenDirectionalLight = new THREE.DirectionalLight(0xffffff, 0.9);
            fullscreenDirectionalLight.position.set(200, 300, 200);
            fullscreenDirectionalLight.castShadow = true;
            fullscreenScene.add(fullscreenDirectionalLight);
            
            // Ground for fullscreen with square pattern
            createFullscreenSquareGround();
            
            // Add distant mountains for fullscreen
            for (let i = 0; i < 8; i++) {
                const distance = 2000 + Math.random() * 1000;
                const angle = (i / 8) * Math.PI * 2;
                
                const x = Math.cos(angle) * distance;
                const z = Math.sin(angle) * distance;
                
                const height = 150 + Math.random() * 300;
                const width = 200 + Math.random() * 300;
                
                const mountainGeometry = new THREE.ConeGeometry(width / 2, height, 8);
                const mountainMaterial = new THREE.MeshLambertMaterial({ 
                    color: 0x556B2F,
                    flatShading: true
                });
                
                const mountain = new THREE.Mesh(mountainGeometry, mountainMaterial);
                mountain.position.set(x, height / 2, z);
                mountain.castShadow = true;
                fullscreenScene.add(mountain);
            }
            
            // Create projectile for fullscreen
            createFullscreenProjectile();
            
            // Create trajectory line for fullscreen
            createFullscreenTrajectoryLine();
            
            // Create launch platform for fullscreen
            createFullscreenLaunchPlatform();
        }
        
        function createFullscreenSquareGround() {
            // Create a grid of squares for fullscreen
            const squareSize = 100;
            const halfSize = 2500;
            
            // Alternate colors for checkerboard pattern
            const color1 = 0x0B2F33;
            const color2 = 0x14393F;
            
            for (let i = -halfSize; i < halfSize; i += squareSize) {
                for (let j = -halfSize; j < halfSize; j += squareSize) {
                    const isEven = ((i + j) / squareSize) % 2 === 0;
                    const squareColor = isEven ? color1 : color2;
                    
                    const squareGeometry = new THREE.PlaneGeometry(squareSize, squareSize);
                    const squareMaterial = new THREE.MeshLambertMaterial({ 
                        color: squareColor,
                        side: THREE.DoubleSide
                    });
                    
                    const square = new THREE.Mesh(squareGeometry, squareMaterial);
                    square.rotation.x = Math.PI / 2;
                    square.position.set(i + squareSize/2, 0.01, j + squareSize/2);
                    square.receiveShadow = true;
                    
                    fullscreenScene.add(square);
                }
            }
        }
        
        function createFullscreenProjectile() {
            // Remove existing projectile
            if (fullscreenProjectile) {
                fullscreenScene.remove(fullscreenProjectile);
                if (fullscreenProjectile.geometry) fullscreenProjectile.geometry.dispose();
                if (fullscreenProjectile.material) fullscreenProjectile.material.dispose();
            }
            
            let geometry;
            const color = new THREE.Color(currentProjectileColor);
            
            switch(currentProjectileShape) {
                case 'sphere':
                    geometry = new THREE.SphereGeometry(3, 32, 32);
                    break;
                case 'cube':
                    geometry = new THREE.BoxGeometry(4, 4, 4);
                    break;
                case 'pyramid':
                    geometry = new THREE.ConeGeometry(2, 4, 4);
                    break;
                case 'cylinder':
                    geometry = new THREE.CylinderGeometry(2, 2, 5, 16);
                    break;
                default:
                    geometry = new THREE.SphereGeometry(3, 32, 32);
            }
            
            const material = new THREE.MeshPhongMaterial({ 
                color: color,
                shininess: 100,
                specular: 0xFFFFFF
            });
            
            fullscreenProjectile = new THREE.Mesh(geometry, material);
            fullscreenProjectile.castShadow = true;
            fullscreenProjectile.position.set(0, launchHeight, 0);
            fullscreenScene.add(fullscreenProjectile);
        }
        
        function createFullscreenTrajectoryLine() {
            // Remove existing trajectory line
            if (fullscreenTrajectoryLine) {
                fullscreenScene.remove(fullscreenTrajectoryLine);
                if (fullscreenTrajectoryLine.geometry) fullscreenTrajectoryLine.geometry.dispose();
                if (fullscreenTrajectoryLine.material) fullscreenTrajectoryLine.material.dispose();
            }
            
            let material;
            const color = new THREE.Color(0xFF4444);
            
            switch(currentTrajectoryStyle) {
                case 'line':
                    material = new THREE.LineBasicMaterial({ 
                        color: color,
                        linewidth: 4,
                        transparent: true,
                        opacity: 0.8
                    });
                    break;
                case 'dashed':
                    material = new THREE.LineDashedMaterial({ 
                        color: color,
                        linewidth: 3,
                        dashSize: 4,
                        gapSize: 3,
                        transparent: true,
                        opacity: 0.8
                    });
                    break;
                case 'points':
                    material = new THREE.PointsMaterial({ 
                        color: color,
                        size: 4,
                        transparent: true,
                        opacity: 0.8
                    });
                    break;
                default:
                    material = new THREE.LineBasicMaterial({ 
                        color: color,
                        linewidth: 4,
                        transparent: true,
                        opacity: 0.8
                    });
            }
            
            const geometry = new THREE.BufferGeometry();
            fullscreenTrajectoryLine = currentTrajectoryStyle === 'points' ? 
                new THREE.Points(geometry, material) : 
                new THREE.Line(geometry, material);
            fullscreenScene.add(fullscreenTrajectoryLine);
        }
        
        function createFullscreenLaunchPlatform() {
            const platformGroup = new THREE.Group();
            
            // Base
            const baseGeometry = new THREE.CylinderGeometry(12, 15, 3, 16);
            const baseMaterial = new THREE.MeshLambertMaterial({ color: 0x2C4A4E });
            const base = new THREE.Mesh(baseGeometry, baseMaterial);
            base.position.y = 1.5;
            platformGroup.add(base);
            
            // Launcher tube
            const tubeGeometry = new THREE.CylinderGeometry(3, 3.5, launchHeight, 8);
            const tubeMaterial = new THREE.MeshLambertMaterial({ color: 0x555555 });
            const tube = new THREE.Mesh(tubeGeometry, tubeMaterial);
            tube.position.y = launchHeight / 2 + 1.5;
            platformGroup.add(tube);
            
            platformGroup.position.set(0, 0, 0);
            fullscreenScene.add(platformGroup);
        }
        
        // ====== FULLSCREEN SIMULATION FUNCTIONS ======
        function startFullscreenSimulation() {
            if (fullscreenSimulationRunning) {
                // If already running, pause it
                fullscreenSimulationPaused = !fullscreenSimulationPaused;
                fullscreenLaunchBtn.innerHTML = fullscreenSimulationPaused ? 
                    '<i class="fas fa-play"></i> Resume' : 
                    '<i class="fas fa-pause"></i> Pause';
                statusMessage.textContent = fullscreenSimulationPaused ? 'Fullscreen Simulation Paused' : 'Fullscreen Simulation Running';
                return;
            }
            
            // Reset simulation variables
            fullscreenSimulationRunning = true;
            fullscreenSimulationPaused = false;
            fullscreenSimulationTime = 0;
            fullscreenSimulationStartTime = 0;
            
            // Reset projectile position
            fullscreenProjectile.position.set(0, launchHeight, 0);
            
            // Clear trajectory line
            fullscreenTrajectoryLine.geometry.dispose();
            fullscreenTrajectoryLine.geometry = new THREE.BufferGeometry();
            
            // Update button state
            fullscreenLaunchBtn.innerHTML = '<i class="fas fa-pause"></i> Pause Simulation';
            statusMessage.textContent = 'Fullscreen Simulation Running';
            
            // Start animation
            fullscreenAnimationId = requestAnimationFrame(updateFullscreenSimulation);
        }
        
        function updateFullscreenSimulation(timestamp) {
            if (!fullscreenSimulationRunning || fullscreenSimulationPaused) return;
            
            if (fullscreenSimulationStartTime === 0) {
                fullscreenSimulationStartTime = timestamp;
            }
            
            const deltaTime = (timestamp - fullscreenSimulationStartTime) / 1000;
            fullscreenSimulationTime = deltaTime;
            
            // Calculate projectile position with advanced physics
            const pos = calculateProjectilePosition(deltaTime);
            
            // Update projectile position and rotation (for spin effect)
            fullscreenProjectile.position.set(pos.x, pos.y, 0);
            if (spinRate > 0) {
                fullscreenProjectile.rotation.x += spinRate * 0.001;
                fullscreenProjectile.rotation.y += spinRate * 0.0005;
            }
            
            // Update trajectory line
            updateFullscreenTrajectoryLine(pos);
            
            // Check if projectile has hit the ground
            if (pos.y <= 0 && fullscreenSimulationTime > 0.1) {
                endFullscreenSimulation();
            }
            
            // Continue animation
            fullscreenAnimationId = requestAnimationFrame(updateFullscreenSimulation);
        }
        
        function updateFullscreenTrajectoryLine(currentPos) {
            const positions = fullscreenTrajectoryLine.geometry.attributes.position;
            
            if (!positions || positions.count >= 1000) {
                const newGeometry = new THREE.BufferGeometry();
                fullscreenTrajectoryLine.geometry = newGeometry;
            }
            
            let points = [];
            if (fullscreenTrajectoryLine.geometry.attributes.position) {
                const array = fullscreenTrajectoryLine.geometry.attributes.position.array;
                for (let i = 0; i < array.length; i += 3) {
                    points.push(new THREE.Vector3(array[i], array[i+1], array[i+2]));
                }
            }
            
            points.push(new THREE.Vector3(currentPos.x, currentPos.y, 0));
            
            const newPositions = new Float32Array(points.length * 3);
            points.forEach((point, i) => {
                newPositions[i * 3] = point.x;
                newPositions[i * 3 + 1] = point.y;
                newPositions[i * 3 + 2] = point.z;
            });
            
            fullscreenTrajectoryLine.geometry.setAttribute('position', new THREE.BufferAttribute(newPositions, 3));
            
            // Update dashed line
            if (currentTrajectoryStyle === 'dashed') {
                fullscreenTrajectoryLine.computeLineDistances();
            }
        }
        
        function endFullscreenSimulation() {
            fullscreenSimulationRunning = false;
            fullscreenSimulationPaused = false;
            
            // Update button state
            fullscreenLaunchBtn.innerHTML = '<i class="fas fa-rocket"></i> Launch Projectile';
            statusMessage.textContent = 'Fullscreen Simulation Complete';
            
            // Cancel animation
            if (fullscreenAnimationId) {
                cancelAnimationFrame(fullscreenAnimationId);
                fullscreenAnimationId = null;
            }
        }
        
        function resetFullscreenSimulation() {
            endFullscreenSimulation();
            
            // Reset projectile position
            fullscreenProjectile.position.set(0, launchHeight, 0);
            fullscreenProjectile.rotation.set(0, 0, 0);
            
            // Clear trajectory line
            fullscreenTrajectoryLine.geometry.dispose();
            fullscreenTrajectoryLine.geometry = new THREE.BufferGeometry();
            
            statusMessage.textContent = 'Fullscreen Simulation Reset';
            setTimeout(() => {
                statusMessage.textContent = 'Advanced Simulation Ready';
            }, 2000);
        }
        
        // ====== PHYSICS SIMULATION FUNCTIONS ======
        function calculateProjectilePosition(time) {
            const angleRad = launchAngle * Math.PI / 180;
            
            // Enhanced physics with drag coefficient, lift force, and spin
            const k = airResistance * dragCoefficient * airDensity;
            const vx0 = initialVelocity * Math.cos(angleRad);
            const vy0 = initialVelocity * Math.sin(angleRad);
            
            // Calculate wind effect
            let windEffectX = 0, windEffectY = 0;
            if (windEnabled && windSpeed > 0) {
                const windRad = windDirection * Math.PI / 180;
                windEffectX = windSpeed * Math.cos(windRad) * time;
                windEffectY = windSpeed * Math.sin(windRad) * time;
            }
            
            // Calculate Magnus effect from spin
            let magnusX = 0, magnusY = 0;
            if (spinRate > 0) {
                const magnusCoefficient = 0.0001 * spinRate;
                magnusX = magnusCoefficient * vy0 * time;
                magnusY = -magnusCoefficient * vx0 * time;
            }
            
            // Calculate lift effect
            let liftEffect = 0;
            if (liftForce > 0) {
                liftEffect = (liftForce / projectileMass) * time * time;
            }
            
            if (k > 0) {
                // With air resistance and advanced effects
                const vx = vx0 * Math.exp(-k * time / projectileMass);
                const vy = (vy0 + (gravity * projectileMass / k)) * Math.exp(-k * time / projectileMass) - (gravity * projectileMass / k);
                
                const x = (vx0 * projectileMass / k) * (1 - Math.exp(-k * time / projectileMass)) + windEffectX + magnusX;
                const y = launchHeight + (projectileMass / k) * ((vy0 + (gravity * projectileMass / k)) * (1 - Math.exp(-k * time / projectileMass)) - gravity * time) + windEffectY + magnusY + liftEffect;
                
                return { x, y, vx, vy };
            } else {
                // Without air resistance (simplified) with advanced effects
                const x = vx0 * time + windEffectX + magnusX;
                const y = launchHeight + vy0 * time - 0.5 * gravity * time * time + windEffectY + magnusY + liftEffect;
                const vx = vx0;
                const vy = vy0 - gravity * time;
                
                return { x, y, vx, vy };
            }
        }
        
        function updateSimulation(timestamp) {
            if (!simulationRunning || simulationPaused) return;
            
            if (simulationStartTime === 0) {
                simulationStartTime = timestamp;
            }
            
            const deltaTime = (timestamp - simulationStartTime) / 1000;
            simulationTime = deltaTime;
            
            // Calculate projectile position with advanced physics
            const pos = calculateProjectilePosition(deltaTime);
            
            // Update projectile position and rotation (for spin effect)
            projectile.position.set(pos.x, pos.y, 0);
            if (spinRate > 0) {
                projectile.rotation.x += spinRate * 0.001;
                projectile.rotation.y += spinRate * 0.0005;
            }
            
            // Update trajectory line
            updateTrajectoryLine(pos);
            
            // Update environmental effects
            updateEnvironmentalEffects();
            
            // Update stats with advanced calculations
            updateAdvancedStats(pos);
            
            // Record data for chart
            simulationData.push({
                time: simulationTime,
                x: pos.x,
                y: pos.y,
                velocity: Math.sqrt(pos.vx * pos.vx + pos.vy * pos.vy)
            });
            
            // Update chart
            updateChart();
            
            // Check if projectile has hit the ground
            if (pos.y <= 0 && simulationTime > 0.1) {
                endSimulation();
                saveToHistory();
            }
            
            // Continue animation
            animationId = requestAnimationFrame(updateSimulation);
        }
        
        function updateTrajectoryLine(currentPos) {
            const positions = trajectoryLine.geometry.attributes.position;
            
            if (!positions || positions.count >= 1000) {
                const newGeometry = new THREE.BufferGeometry();
                trajectoryLine.geometry = newGeometry;
            }
            
            let points = [];
            if (trajectoryLine.geometry.attributes.position) {
                const array = trajectoryLine.geometry.attributes.position.array;
                for (let i = 0; i < array.length; i += 3) {
                    points.push(new THREE.Vector3(array[i], array[i+1], array[i+2]));
                }
            }
            
            points.push(new THREE.Vector3(currentPos.x, currentPos.y, 0));
            
            const newPositions = new Float32Array(points.length * 3);
            points.forEach((point, i) => {
                newPositions[i * 3] = point.x;
                newPositions[i * 3 + 1] = point.y;
                newPositions[i * 3 + 2] = point.z;
            });
            
            trajectoryLine.geometry.setAttribute('position', new THREE.BufferAttribute(newPositions, 3));
            
            // Update dashed line
            if (currentTrajectoryStyle === 'dashed') {
                trajectoryLine.computeLineDistances();
            }
        }
        
        function updateAdvancedStats(pos) {
            // Calculate current values
            currentHeight = pos.y;
            currentDistance = pos.x;
            currentVelocity = Math.sqrt(pos.vx * pos.vx + pos.vy * pos.vy);
            
            // Calculate forces and energy
            const velocityMagnitude = currentVelocity;
            kineticEnergy = 0.5 * projectileMass * velocityMagnitude * velocityMagnitude;
            dragForce = 0.5 * airDensity * dragCoefficient * velocityMagnitude * velocityMagnitude;
            weightForce = projectileMass * gravity;
            
            // Update peak height
            if (currentHeight > peakHeight) {
                peakHeight = currentHeight;
                timeToPeak = simulationTime;
            }
            
            // Update max range
            if (currentDistance > maxRange) {
                maxRange = currentDistance;
            }
            
            // Calculate theoretical values
            const angleRad = launchAngle * Math.PI / 180;
            const theoreticalMaxRange = (initialVelocity * initialVelocity * Math.sin(2 * angleRad)) / gravity;
            
            // Calculate energy efficiency
            const initialEnergy = 0.5 * projectileMass * initialVelocity * initialVelocity;
            energyEfficiency = (kineticEnergy / initialEnergy) * 100;
            
            // Calculate impact energy
            impactEnergy = kineticEnergy;
            
            // Update DOM elements
            currentHeightEl.textContent = currentHeight.toFixed(1) + " m";
            currentDistanceEl.textContent = currentDistance.toFixed(1) + " m";
            flightTimeEl.textContent = simulationTime.toFixed(2) + " s";
            peakHeightEl.textContent = peakHeight.toFixed(1) + " m";
            maxRangeEl.textContent = theoreticalMaxRange.toFixed(1) + " m";
            currentVelocityEl.textContent = currentVelocity.toFixed(1) + " m/s";
            timeToPeakEl.textContent = timeToPeak.toFixed(2) + " s";
            kineticEnergyEl.textContent = (kineticEnergy / 1000).toFixed(1) + " kJ";
            dragForceEl.textContent = dragForce.toFixed(1) + " N";
            weightForceEl.textContent = weightForce.toFixed(1) + " N";
            impactEnergyEl.textContent = (impactEnergy / 1000).toFixed(1) + " kJ";
            energyEfficiencyEl.textContent = energyEfficiency.toFixed(1) + " %";
        }
        
        function saveToHistory() {
            const simulationRecord = {
                id: Date.now(),
                timestamp: new Date().toLocaleTimeString(),
                parameters: {
                    velocity: initialVelocity,
                    angle: launchAngle,
                    mass: projectileMass,
                    gravity: gravity,
                    airResistance: airResistance,
                    height: launchHeight
                },
                results: {
                    maxRange: maxRange,
                    peakHeight: peakHeight,
                    flightTime: simulationTime,
                    impactEnergy: impactEnergy
                }
            };
            
            simulationHistory.unshift(simulationRecord);
            if (simulationHistory.length > 10) {
                simulationHistory.pop();
            }
            
            updateHistoryList();
        }
        
        function updateHistoryList() {
            historyList.innerHTML = '';
            
            simulationHistory.forEach((record, index) => {
                const historyItem = document.createElement('div');
                historyItem.className = 'history-item';
                if (index === 0) historyItem.classList.add('active');
                
                historyItem.innerHTML = `
                    <div style="display: flex; justify-content: space-between; align-items: center;">
                        <strong>Simulation ${simulationHistory.length - index}</strong>
                        <span style="font-size: 12px; opacity: 0.7;">${record.timestamp}</span>
                    </div>
                    <div class="history-stats">
                        <div>Velocity: ${record.parameters.velocity} m/s</div>
                        <div>Angle: ${record.parameters.angle}°</div>
                        <div>Range: ${record.results.maxRange.toFixed(1)} m</div>
                        <div>Height: ${record.results.peakHeight.toFixed(1)} m</div>
                        <div>Time: ${record.results.flightTime.toFixed(2)} s</div>
                        <div>Energy: ${(record.results.impactEnergy / 1000).toFixed(1)} kJ</div>
                    </div>
                `;
                
                historyItem.addEventListener('click', () => {
                    document.querySelectorAll('.history-item').forEach(item => item.classList.remove('active'));
                    historyItem.classList.add('active');
                    loadFromHistory(record);
                });
                
                historyList.appendChild(historyItem);
            });
        }
        
        function loadFromHistory(record) {
            // Load parameters from history
            initialVelocity = record.parameters.velocity;
            launchAngle = record.parameters.angle;
            projectileMass = record.parameters.mass;
            gravity = record.parameters.gravity;
            airResistance = record.parameters.airResistance;
            launchHeight = record.parameters.height;
            
            // Update UI controls
            updateControlValues();
            
            // Update 3D scene
            createProjectile();
            createFullscreenProjectile();
            
            // Update status
            statusMessage.textContent = `Loaded simulation from ${record.timestamp}`;
            setTimeout(() => {
                statusMessage.textContent = 'Advanced Simulation Ready';
            }, 3000);
        }
        
        function startSimulation() {
            if (simulationRunning) {
                // If already running, pause it
                simulationPaused = !simulationPaused;
                pauseButton.innerHTML = simulationPaused ? 
                    '<i class="fas fa-play"></i> Resume' : 
                    '<i class="fas fa-pause"></i> Pause';
                statusMessage.textContent = simulationPaused ? 'Simulation Paused' : 'Simulation Running';
                return;
            }
            
            // Reset simulation variables
            simulationRunning = true;
            simulationPaused = false;
            simulationTime = 0;
            simulationStartTime = 0;
            simulationData = [];
            peakHeight = 0;
            maxRange = 0;
            timeToPeak = 0;
            
            // Reset projectile position
            projectile.position.set(0, launchHeight, 0);
            
            // Clear trajectory line
            trajectoryLine.geometry.dispose();
            trajectoryLine.geometry = new THREE.BufferGeometry();
            
            // Clear chart data
            if (trajectoryChart) {
                trajectoryChart.data.datasets[0].data = [];
                trajectoryChart.update();
            }
            
            // Update button states
            launchButton.innerHTML = '<i class="fas fa-pause"></i> Pause Simulation';
            pauseButton.innerHTML = '<i class="fas fa-pause"></i> Pause';
            statusMessage.textContent = 'Simulation Running';
            
            // Start animation
            animationId = requestAnimationFrame(updateSimulation);
        }
        
        function endSimulation() {
            simulationRunning = false;
            simulationPaused = false;
            
            // Update button states
            launchButton.innerHTML = '<i class="fas fa-rocket"></i> Launch Projectile';
            pauseButton.innerHTML = '<i class="fas fa-pause"></i> Pause';
            statusMessage.textContent = 'Simulation Complete';
            
            // Cancel animation
            if (animationId) {
                cancelAnimationFrame(animationId);
                animationId = null;
            }
        }
        
        function resetSimulation() {
            endSimulation();
            
            // Reset projectile position
            projectile.position.set(0, launchHeight, 0);
            projectile.rotation.set(0, 0, 0);
            
            // Clear trajectory line
            trajectoryLine.geometry.dispose();
            trajectoryLine.geometry = new THREE.BufferGeometry();
            
            // Reset stats
            currentHeightEl.textContent = "0.0 m";
            currentDistanceEl.textContent = "0.0 m";
            flightTimeEl.textContent = "0.0 s";
            peakHeightEl.textContent = "0.0 m";
            maxRangeEl.textContent = "0.0 m";
            currentVelocityEl.textContent = "0.0 m/s";
            timeToPeakEl.textContent = "0.0 s";
            kineticEnergyEl.textContent = "0.0 kJ";
            dragForceEl.textContent = "0.0 N";
            weightForceEl.textContent = (projectileMass * gravity).toFixed(1) + " N";
            impactEnergyEl.textContent = "0.0 kJ";
            energyEfficiencyEl.textContent = "0.0 %";
            
            // Reset simulation data
            simulationData = [];
            
            // Reset chart
            if (trajectoryChart) {
                trajectoryChart.data.datasets[0].data = [];
                trajectoryChart.update();
            }
            
            statusMessage.textContent = 'Simulation Reset';
            setTimeout(() => {
                statusMessage.textContent = 'Advanced Simulation Ready';
            }, 2000);
        }
        
        function resetAllParameters() {
            // Reset to default values
            initialVelocity = 50;
            launchAngle = 45;
            projectileMass = 10;
            gravity = 9.81;
            airResistance = 0.1;
            launchHeight = 10;
            windSpeed = 0;
            windDirection = 0;
            windEnabled = true;
            dragCoefficient = 0.47;
            liftForce = 0;
            spinRate = 0;
            airDensity = 1.2;
            rainEffect = false;
            snowEffect = false;
            fogDensity = 0;
            currentProjectileShape = 'sphere';
            currentProjectileColor = '#9370DB';
            currentTrajectoryStyle = 'line';
            currentView = 'perspective';
            
            // Update UI elements
            updateControlValues();
            updateAdvancedControls();
            updateWindControls();
            updateEnvironmentalControls();
            updateProjectileCustomization();
            updateViewControls();
            
            // Reset simulation
            resetSimulation();
            resetFullscreenSimulation();
            
            // Update 3D scene
            createProjectile();
            createTrajectoryLine();
            createFullscreenProjectile();
            createFullscreenTrajectoryLine();
            updateFogEffect();
            setCameraView(currentView);
            
            // Update status
            statusMessage.textContent = 'All parameters reset to defaults';
            setTimeout(() => {
                statusMessage.textContent = 'Advanced Simulation Ready';
            }, 2000);
        }
        
        function updateAdvancedControls() {
            dragCoefficientSlider.value = dragCoefficient;
            dragValue.textContent = dragCoefficient.toFixed(2);
            
            liftForceSlider.value = liftForce;
            liftValue.textContent = liftForce.toFixed(1) + ' N';
            
            spinRateSlider.value = spinRate;
            spinValue.textContent = spinRate + ' rpm';
            
            airDensitySlider.value = airDensity;
            densityValue.textContent = airDensity.toFixed(1) + ' kg/m³';
            
            fogDensitySlider.value = fogDensity;
            fogValue.textContent = fogDensity + '%';
        }
        
        function updateEnvironmentalControls() {
            rainEffectCheckbox.checked = rainEffect;
            snowEffectCheckbox.checked = snowEffect;
        }
        
        function updateProjectileCustomization() {
            // Update shape buttons
            shapeButtons.forEach(btn => {
                btn.classList.remove('active');
                if (btn.dataset.shape === currentProjectileShape) {
                    btn.classList.add('active');
                }
            });
            
            // Update color buttons
            colorButtons.forEach(btn => {
                btn.classList.remove('active');
                if (btn.dataset.color === currentProjectileColor) {
                    btn.classList.add('active');
                }
            });
            
            // Update trajectory buttons
            trajectoryButtons.forEach(btn => {
                btn.classList.remove('active');
                if (btn.dataset.trajectory === currentTrajectoryStyle) {
                    btn.classList.add('active');
                }
            });
        }
        
        function updateViewControls() {
            viewButtons.forEach(btn => {
                btn.classList.remove('active');
                if (btn.dataset.view === currentView) {
                    btn.classList.add('active');
                }
            });
        }
        
        // ====== WIND CONTROL FUNCTIONS ======
        function updateWindControls() {
            windDirectionSlider.value = windDirection;
            windSpeedSlider.value = windSpeed;
            windSpeedInput.value = windSpeed;
            
            // Update wind direction display
            const directionText = getWindDirectionText(windDirection);
            windDirectionValue.textContent = `${windDirection}° (${directionText})`;
            
            // Update wind speed display
            windSpeedValue.textContent = windSpeed.toFixed(1) + ' m/s';
            
            if (windSpeed === 0) {
                windDisplay.textContent = "No Wind";
                windDisplay.style.color = "";
            } else {
                windDisplay.textContent = `${windSpeed.toFixed(1)} m/s from ${directionText}`;
                windDisplay.style.color = "var(--button-deep-violet)";
            }
            
            // Update wind vector for physics
            const windRad = windDirection * Math.PI / 180;
            windVector.x = windSpeed * Math.cos(windRad);
            windVector.y = windSpeed * Math.sin(windRad);
            
            // Update wind visualization
            updateWindVisualization();
        }
        
        function getWindDirectionText(degrees) {
            const directions = ['East', 'North-East', 'North', 'North-West', 'West', 'South-West', 'South', 'South-East'];
            const index = Math.round((degrees % 360) / 45) % 8;
            return directions[index];
        }
        
        // ====== CHART.JS INITIALIZATION ======
        function initChartJS() {
            const ctx = document.getElementById('trajectory-chart').getContext('2d');
            
            trajectoryChart = new Chart(ctx, {
                type: 'line',
                data: {
                    datasets: [{
                        label: 'Projectile Trajectory',
                        data: [],
                        borderColor: '#FF4444',
                        backgroundColor: 'rgba(255, 68, 68, 0.1)',
                        borderWidth: 3,
                        fill: true,
                        tension: 0.4,
                        pointRadius: 0
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        x: {
                            type: 'linear',
                            position: 'bottom',
                            title: {
                                display: true,
                                text: 'Distance (m)',
                                color: '#0B2F33'
                            },
                            grid: {
                                color: 'rgba(11, 47, 51, 0.1)'
                            },
                            ticks: {
                                color: '#0B2F33'
                            }
                        },
                        y: {
                            title: {
                                display: true,
                                text: 'Height (m)',
                                color: '#0B2F33'
                            },
                            grid: {
                                color: 'rgba(11, 47, 51, 0.1)'
                            },
                            ticks: {
                                color: '#0B2F33'
                            }
                        }
                    },
                    plugins: {
                        legend: {
                            labels: {
                                color: '#0B2F33',
                                font: {
                                    size: 14,
                                    weight: 'bold'
                                }
                            }
                        },
                        tooltip: {
                            mode: 'index',
                            intersect: false,
                            backgroundColor: 'rgba(11, 47, 51, 0.9)',
                            titleColor: '#FFFFFF',
                            bodyColor: '#FFFFFF'
                        }
                    }
                }
            });
        }
        
        function updateChart() {
            if (simulationData.length > 0 && trajectoryChart) {
                const lastPoint = simulationData[simulationData.length - 1];
                trajectoryChart.data.datasets[0].data.push({
                    x: lastPoint.x,
                    y: lastPoint.y
                });
                
                if (trajectoryChart.data.datasets[0].data.length > 500) {
                    trajectoryChart.data.datasets[0].data.shift();
                }
                
                trajectoryChart.update('none');
            }
        }
        
        // ====== UI CONTROL FUNCTIONS ======
        function updateControlValues() {
            // Update sliders and inputs
            velocitySlider.value = initialVelocity;
            velocityInput.value = initialVelocity;
            velocityValue.textContent = initialVelocity + ' m/s';
            
            angleSlider.value = launchAngle;
            angleInput.value = launchAngle;
            angleValue.textContent = launchAngle + '°';
            
            massSlider.value = projectileMass;
            massInput.value = projectileMass;
            massValue.textContent = projectileMass + ' kg';
            
            gravitySlider.value = gravity;
            gravityInput.value = gravity;
            gravityValue.textContent = gravity.toFixed(2) + ' m/s²';
            
            resistanceSlider.value = airResistance;
            resistanceInput.value = airResistance;
            resistanceValue.textContent = airResistance.toFixed(2);
            
            heightSlider.value = launchHeight;
            heightInput.value = launchHeight;
            heightValue.textContent = launchHeight + ' m';
            
            // Update weight force display
            weightForceEl.textContent = (projectileMass * gravity).toFixed(1) + ' N';
            
            // Reset simulation when parameters change
            resetSimulation();
        }
        
        function applyPreset(presetName) {
            const presets = {
                cannon: { velocity: 150, angle: 30, mass: 20, gravity: 9.81, resistance: 0.05, height: 5 },
                mortar: { velocity: 80, angle: 70, mass: 15, gravity: 9.81, resistance: 0.1, height: 0 },
                sniper: { velocity: 900, angle: 5, mass: 0.05, gravity: 9.81, resistance: 0.005, height: 1.5 },
                space: { velocity: 500, angle: 45, mass: 1000, gravity: 3.7, resistance: 0.001, height: 0 },
                maxrange: { velocity: 100, angle: 45, mass: 10, gravity: 9.81, resistance: 0, height: 0 },
                moon: { velocity: 50, angle: 45, mass: 10, gravity: 1.62, resistance: 0, height: 0 },
                jupiter: { velocity: 50, angle: 45, mass: 10, gravity: 24.79, resistance: 0.2, height: 0 },
                parabolic: { velocity: 75, angle: 45, mass: 5, gravity: 9.81, resistance: 0, height: 0 }
            };
            
            if (presets[presetName]) {
                const preset = presets[presetName];
                initialVelocity = preset.velocity;
                launchAngle = preset.angle;
                projectileMass = preset.mass;
                gravity = preset.gravity;
                airResistance = preset.resistance;
                launchHeight = preset.height;
                
                updateControlValues();
                statusMessage.textContent = `Applied ${presetName} preset`;
                setTimeout(() => {
                    statusMessage.textContent = 'Advanced Simulation Ready';
                }, 2000);
            }
        }
        
        function randomizeParameters() {
            initialVelocity = Math.floor(Math.random() * (200 - 20 + 1)) + 20;
            launchAngle = Math.floor(Math.random() * (80 - 10 + 1)) + 10;
            projectileMass = Math.floor(Math.random() * (50 - 1 + 1)) + 1;
            gravity = (Math.random() * (20 - 3 + 1) + 3).toFixed(2);
            airResistance = (Math.random() * 0.3).toFixed(3);
            launchHeight = Math.floor(Math.random() * (50 - 0 + 1)) + 0;
            windSpeed = (Math.random() * 30).toFixed(1);
            windDirection = Math.floor(Math.random() * 360);
            dragCoefficient = (Math.random() * 0.5 + 0.1).toFixed(2);
            liftForce = (Math.random() * 5).toFixed(1);
            spinRate = Math.floor(Math.random() * 500);
            airDensity = (Math.random() * 1 + 0.5).toFixed(1);
            fogDensity = Math.floor(Math.random() * 50);
            
            // Randomize projectile appearance
            const shapes = ['sphere', 'cube', 'pyramid', 'cylinder'];
            const colors = ['#9370DB', '#FF4444', '#4B0082', '#F26A2E', '#2E8B57', '#DAA520'];
            const trajectories = ['line', 'dashed', 'points'];
            
            currentProjectileShape = shapes[Math.floor(Math.random() * shapes.length)];
            currentProjectileColor = colors[Math.floor(Math.random() * colors.length)];
            currentTrajectoryStyle = trajectories[Math.floor(Math.random() * trajectories.length)];
            
            updateControlValues();
            updateAdvancedControls();
            updateWindControls();
            updateEnvironmentalControls();
            updateProjectileCustomization();
            
            // Update 3D scene
            createProjectile();
            createTrajectoryLine();
            createFullscreenProjectile();
            createFullscreenTrajectoryLine();
            updateFogEffect();
            
            statusMessage.textContent = 'Parameters randomized';
            setTimeout(() => {
                statusMessage.textContent = 'Advanced Simulation Ready';
            }, 2000);
        }
        
        function exportData() {
            if (simulationData.length === 0) {
                alert('No simulation data to export. Please run a simulation first.');
                return;
            }
            
            let csvContent = "Time (s),Distance (m),Height (m),Velocity (m/s),Kinetic Energy (J),Drag Force (N)\n";
            
            simulationData.forEach(point => {
                const ke = 0.5 * projectileMass * point.velocity * point.velocity;
                const drag = 0.5 * airDensity * dragCoefficient * point.velocity * point.velocity;
                csvContent += `${point.time.toFixed(3)},${point.x.toFixed(3)},${point.y.toFixed(3)},${point.velocity.toFixed(3)},${ke.toFixed(3)},${drag.toFixed(3)}\n`;
            });
            
            const blob = new Blob([csvContent], { type: 'text/csv' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `advanced_projectile_simulation_${Date.now()}.csv`;
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
            
            statusMessage.textContent = 'CSV data exported successfully';
            setTimeout(() => {
                statusMessage.textContent = 'Advanced Simulation Ready';
            }, 2000);
        }
        
        function exportJsonData() {
            if (simulationData.length === 0) {
                alert('No simulation data to export. Please run a simulation first.');
                return;
            }
            
            const simulationExport = {
                metadata: {
                    version: '4.0',
                    exportDate: new Date().toISOString(),
                    simulator: 'SIT Advanced Projectile Motion Simulator'
                },
                parameters: {
                    initialVelocity: initialVelocity,
                    launchAngle: launchAngle,
                    projectileMass: projectileMass,
                    gravity: gravity,
                    airResistance: airResistance,
                    launchHeight: launchHeight,
                    dragCoefficient: dragCoefficient,
                    liftForce: liftForce,
                    spinRate: spinRate,
                    airDensity: airDensity,
                    windSpeed: windSpeed,
                    windDirection: windDirection
                },
                simulationData: simulationData,
                results: {
                    maxRange: maxRange,
                    peakHeight: peakHeight,
                    flightTime: simulationTime,
                    impactEnergy: impactEnergy,
                    energyEfficiency: energyEfficiency
                }
            };
            
            const jsonData = JSON.stringify(simulationExport, null, 2);
            const blob = new Blob([jsonData], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `advanced_projectile_simulation_${Date.now()}.json`;
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
            
            statusMessage.textContent = 'JSON data exported successfully';
            setTimeout(() => {
                statusMessage.textContent = 'Advanced Simulation Ready';
            }, 2000);
        }
        
        // ====== ABOUT APPLICATION FUNCTIONS ======
        function showAboutSection() {
            aboutSection.style.display = 'block';
            aboutSection.scrollIntoView({ behavior: 'smooth' });
            statusMessage.textContent = 'About Application';
        }
        
        function hideAboutSection() {
            aboutSection.style.display = 'none';
            statusMessage.textContent = 'Advanced Simulation Ready';
        }
        
        // ====== FULLSCREEN MODE FUNCTIONS ======
        function enterFullscreenMode() {
            fullscreenMode = true;
            fullscreenOverlay.style.display = 'flex';
            document.body.style.overflow = 'hidden';
            
            // Update canvas size
            fullscreenCamera.aspect = window.innerWidth / window.innerHeight;
            fullscreenCamera.updateProjectionMatrix();
            fullscreenRenderer.setSize(window.innerWidth, window.innerHeight);
            
            // Reset camera position
            fullscreenCamera.position.set(200, 150, 300);
            fullscreenControls.update();
            
            // Update projectile and trajectory in fullscreen
            createFullscreenProjectile();
            createFullscreenTrajectoryLine();
            
            // Update wind indicator
            updateWindVisualization();
            
            statusMessage.textContent = 'Entered fullscreen mode';
        }
        
        function exitFullscreenMode() {
            fullscreenMode = false;
            fullscreenOverlay.style.display = 'none';
            document.body.style.overflow = 'auto';
            
            // Stop any running simulation in fullscreen
            if (fullscreenSimulationRunning) {
                endFullscreenSimulation();
            }
            
            statusMessage.textContent = 'Exited fullscreen mode';
            setTimeout(() => {
                statusMessage.textContent = 'Advanced Simulation Ready';
            }, 2000);
        }
        
        // ====== ANIMATION LOOPS ======
        function animate() {
            requestAnimationFrame(animate);
            
            // Update environmental effects
            updateEnvironmentalEffects();
            
            controls.update();
            renderer.render(scene, camera);
        }
        
        function animateFullscreen() {
            requestAnimationFrame(animateFullscreen);
            if (fullscreenMode) {
                fullscreenControls.update();
                fullscreenRenderer.render(fullscreenScene, fullscreenCamera);
            }
        }
        
        // ====== EVENT LISTENERS ======
        function setupEventListeners() {
            // Velocity controls
            velocitySlider.addEventListener('input', function() {
                initialVelocity = parseInt(this.value);
                updateControlValues();
            });
            
            velocityInput.addEventListener('change', function() {
                let value = parseInt(this.value);
                if (value < 10) value = 10;
                if (value > 200) value = 200;
                initialVelocity = value;
                updateControlValues();
            });
            
            // Angle controls
            angleSlider.addEventListener('input', function() {
                launchAngle = parseInt(this.value);
                updateControlValues();
            });
            
            angleInput.addEventListener('change', function() {
                let value = parseInt(this.value);
                if (value < 0) value = 0;
                if (value > 90) value = 90;
                launchAngle = value;
                updateControlValues();
            });
            
            // Mass controls
            massSlider.addEventListener('input', function() {
                projectileMass = parseInt(this.value);
                updateControlValues();
            });
            
            massInput.addEventListener('change', function() {
                let value = parseInt(this.value);
                if (value < 1) value = 1;
                if (value > 100) value = 100;
                projectileMass = value;
                updateControlValues();
            });
            
            // Gravity controls
            gravitySlider.addEventListener('input', function() {
                gravity = parseFloat(this.value);
                updateControlValues();
            });
            
            gravityInput.addEventListener('change', function() {
                let value = parseFloat(this.value);
                if (value < 1.62) value = 1.62;
                if (value > 24.79) value = 24.79;
                gravity = value;
                updateControlValues();
            });
            
            // Air resistance controls
            resistanceSlider.addEventListener('input', function() {
                airResistance = parseFloat(this.value);
                updateControlValues();
            });
            
            resistanceInput.addEventListener('change', function() {
                let value = parseFloat(this.value);
                if (value < 0) value = 0;
                if (value > 0.5) value = 0.5;
                airResistance = value;
                updateControlValues();
            });
            
            // Height controls
            heightSlider.addEventListener('input', function() {
                launchHeight = parseInt(this.value);
                updateControlValues();
            });
            
            heightInput.addEventListener('change', function() {
                let value = parseInt(this.value);
                if (value < 0) value = 0;
                if (value > 100) value = 100;
                launchHeight = value;
                updateControlValues();
            });
            
            // Advanced controls
            dragCoefficientSlider.addEventListener('input', function() {
                dragCoefficient = parseFloat(this.value);
                dragValue.textContent = dragCoefficient.toFixed(2);
                resetSimulation();
            });
            
            liftForceSlider.addEventListener('input', function() {
                liftForce = parseFloat(this.value);
                liftValue.textContent = liftForce.toFixed(1) + ' N';
                resetSimulation();
            });
            
            spinRateSlider.addEventListener('input', function() {
                spinRate = parseInt(this.value);
                spinValue.textContent = spinRate + ' rpm';
                resetSimulation();
            });
            
            airDensitySlider.addEventListener('input', function() {
                airDensity = parseFloat(this.value);
                densityValue.textContent = airDensity.toFixed(1) + ' kg/m³';
                resetSimulation();
            });
            
            // Environmental controls
            rainEffectCheckbox.addEventListener('change', function() {
                rainEffect = this.checked;
                if (rainEffect) {
                    createRainEffect();
                } else {
                    // Remove rain particles
                    rainParticles.forEach(rain => scene.remove(rain));
                    rainParticles = [];
                }
            });
            
            snowEffectCheckbox.addEventListener('change', function() {
                snowEffect = this.checked;
                if (snowEffect) {
                    createSnowEffect();
                } else {
                    // Remove snow particles
                    snowParticles.forEach(snow => scene.remove(snow));
                    snowParticles = [];
                }
            });
            
            fogDensitySlider.addEventListener('input', function() {
                fogDensity = parseInt(this.value);
                fogValue.textContent = fogDensity + '%';
                updateFogEffect();
            });
            
            // Wind controls
            windDirectionSlider.addEventListener('input', function() {
                windDirection = parseInt(this.value);
                updateWindControls();
            });
            
            windSpeedSlider.addEventListener('input', function() {
                windSpeed = parseFloat(this.value);
                updateWindControls();
            });
            
            windSpeedInput.addEventListener('change', function() {
                let value = parseFloat(this.value);
                if (value < 0) value = 0;
                if (value > 50) value = 50;
                windSpeed = value;
                updateWindControls();
            });
            
            // Launch button
            launchButton.addEventListener('click', startSimulation);
            
            // Multi launch button
            multiLaunchButton.addEventListener('click', function() {
                // Launch multiple projectiles with slight variations
                for (let i = 0; i < 3; i++) {
                    setTimeout(() => {
                        const originalVelocity = initialVelocity;
                        const originalAngle = launchAngle;
                        
                        // Slightly vary parameters
                        initialVelocity = originalVelocity * (0.9 + Math.random() * 0.2);
                        launchAngle = Math.max(10, Math.min(80, originalAngle + (Math.random() * 10 - 5)));
                        
                        updateControlValues();
                        startSimulation();
                        
                        // Restore original parameters after a delay
                        setTimeout(() => {
                            initialVelocity = originalVelocity;
                            launchAngle = originalAngle;
                            updateControlValues();
                        }, 100);
                    }, i * 500);
                }
                
                statusMessage.textContent = 'Launching multiple projectiles...';
            });
            
            // Reset button
            resetButton.addEventListener('click', resetSimulation);
            
            // Fullscreen toggle
            fullscreenToggle.addEventListener('click', enterFullscreenMode);
            
            // Exit fullscreen
            exitFullscreenBtn.addEventListener('click', exitFullscreenMode);
            
            // Fullscreen launch button
            fullscreenLaunchBtn.addEventListener('click', startFullscreenSimulation);
            
            // Fullscreen reset button
            fullscreenResetBtn.addEventListener('click', resetFullscreenSimulation);
            
            // Randomize button
            randomizeButton.addEventListener('click', randomizeParameters);
            
            // Pause button
            pauseButton.addEventListener('click', function() {
                if (simulationRunning) {
                    simulationPaused = !simulationPaused;
                    this.innerHTML = simulationPaused ? 
                        '<i class="fas fa-play"></i> Resume' : 
                        '<i class="fas fa-pause"></i> Pause';
                    launchButton.innerHTML = simulationPaused ? 
                        '<i class="fas fa-play"></i> Resume Simulation' : 
                        '<i class="fas fa-pause"></i> Pause Simulation';
                    statusMessage.textContent = simulationPaused ? 'Simulation Paused' : 'Simulation Running';
                }
            });
            
            // Reset all button
            resetAllButton.addEventListener('click', resetAllParameters);
            
            // Reset defaults button
            resetDefaultsButton.addEventListener('click', resetAllParameters);
            
            // Save preset button
            savePresetButton.addEventListener('click', function() {
                const presetName = prompt('Enter a name for this preset:', 'Custom Preset');
                if (presetName) {
                    // In a real implementation, you would save this to localStorage
                    statusMessage.textContent = `Preset "${presetName}" saved (local storage not implemented in demo)`;
                    setTimeout(() => {
                        statusMessage.textContent = 'Advanced Simulation Ready';
                    }, 2000);
                }
            });
            
            // History replay button
            historyReplayButton.addEventListener('click', function() {
                if (simulationHistory.length > 0) {
                    const latestSimulation = simulationHistory[0];
                    loadFromHistory(latestSimulation);
                    setTimeout(() => {
                        startSimulation();
                    }, 1000);
                } else {
                    statusMessage.textContent = 'No simulation history available';
                    setTimeout(() => {
                        statusMessage.textContent = 'Advanced Simulation Ready';
                    }, 2000);
                }
            });
            
            // Export buttons
            exportButton.addEventListener('click', exportData);
            exportJsonButton.addEventListener('click', exportJsonData);
            
            // About button
            aboutButton.addEventListener('click', showAboutSection);
            closeAboutButton.addEventListener('click', hideAboutSection);
            
            // View control buttons
            viewButtons.forEach(button => {
                button.addEventListener('click', function() {
                    const view = this.dataset.view;
                    setCameraView(view);
                    
                    // Update active state
                    viewButtons.forEach(btn => btn.classList.remove('active'));
                    this.classList.add('active');
                    
                    statusMessage.textContent = `Switched to ${view} view`;
                    setTimeout(() => {
                        statusMessage.textContent = 'Advanced Simulation Ready';
                    }, 2000);
                });
            });
            
            // Projectile customization
            shapeButtons.forEach(button => {
                button.addEventListener('click', function() {
                    currentProjectileShape = this.dataset.shape;
                    createProjectile();
                    createFullscreenProjectile();
                    
                    shapeButtons.forEach(btn => btn.classList.remove('active'));
                    this.classList.add('active');
                    
                    statusMessage.textContent = `Projectile shape changed to ${currentProjectileShape}`;
                    setTimeout(() => {
                        statusMessage.textContent = 'Advanced Simulation Ready';
                    }, 2000);
                });
            });
            
            colorButtons.forEach(button => {
                button.addEventListener('click', function() {
                    currentProjectileColor = this.dataset.color;
                    createProjectile();
                    createFullscreenProjectile();
                    
                    colorButtons.forEach(btn => btn.classList.remove('active'));
                    this.classList.add('active');
                    
                    statusMessage.textContent = 'Projectile color changed';
                    setTimeout(() => {
                        statusMessage.textContent = 'Advanced Simulation Ready';
                    }, 2000);
                });
            });
            
            trajectoryButtons.forEach(button => {
                button.addEventListener('click', function() {
                    currentTrajectoryStyle = this.dataset.trajectory;
                    createTrajectoryLine();
                    createFullscreenTrajectoryLine();
                    
                    trajectoryButtons.forEach(btn => btn.classList.remove('active'));
                    this.classList.add('active');
                    
                    statusMessage.textContent = `Trajectory style changed to ${currentTrajectoryStyle}`;
                    setTimeout(() => {
                        statusMessage.textContent = 'Advanced Simulation Ready';
                    }, 2000);
                });
            });
            
            // Preset buttons
            presetButtons.forEach(button => {
                button.addEventListener('click', function() {
                    const presetName = this.getAttribute('data-preset');
                    applyPreset(presetName);
                });
            });
            
            // Handle window resize
            window.addEventListener('resize', function() {
                // Update main canvas
                const canvas = document.getElementById('simulation-canvas');
                const canvasWidth = canvas.parentElement.clientWidth;
                const canvasHeight = canvas.parentElement.clientHeight;
                
                camera.aspect = canvasWidth / canvasHeight;
                camera.updateProjectionMatrix();
                renderer.setSize(canvasWidth, canvasHeight);
                
                // Update fullscreen canvas if in fullscreen mode
                if (fullscreenMode) {
                    fullscreenCamera.aspect = window.innerWidth / window.innerHeight;
                    fullscreenCamera.updateProjectionMatrix();
                    fullscreenRenderer.setSize(window.innerWidth, window.innerHeight);
                }
                
                // Update chart
                if (trajectoryChart) {
                    trajectoryChart.resize();
                }
                
                // Update planetary viewer
                onPlanetaryResize();
            });
            
            // Handle ESC key to exit fullscreen
            document.addEventListener('keydown', function(event) {
                if (event.key === 'Escape' && fullscreenMode) {
                    exitFullscreenMode();
                }
            });
            
            // ====== PLANETARY EVENT LISTENERS ======
            // Planet selection
            planetCards.forEach(card => {
                card.addEventListener('click', function() {
                    planetCards.forEach(c => c.classList.remove('active'));
                    this.classList.add('active');
                    currentPlanet = this.dataset.planet;
                    createPlanetaryPlanet(currentPlanet);
                    const velocity = parseInt(planetVelocitySlider.value);
                    const angle = parseInt(planetAngleSlider.value);
                    updatePlanetaryTrajectoryStats(planets[currentPlanet], velocity, angle);
                });
            });
            
            // Velocity slider
            planetVelocitySlider.addEventListener('input', function() {
                planetVelocityValue.textContent = this.value + ' m/s';
                updatePlanetaryTrajectoryStats(planets[currentPlanet], parseInt(this.value), 
                    parseInt(planetAngleSlider.value));
            });
            
            // Angle slider
            planetAngleSlider.addEventListener('input', function() {
                planetAngleValue.textContent = this.value + '°';
                updatePlanetaryTrajectoryStats(planets[currentPlanet], 
                    parseInt(planetVelocitySlider.value), parseInt(this.value));
            });
            
            // Simulate all planets button
            planetSimulateBtn.addEventListener('click', function() {
                calculateAllPlanets();
                statusMessage.textContent = 'Planetary comparison simulated';
                setTimeout(() => {
                    statusMessage.textContent = 'Advanced Simulation Ready';
                }, 2000);
            });
            
            // View controls
            rotateViewBtn.addEventListener('click', function() {
                planetaryControls.autoRotate = !planetaryControls.autoRotate;
                planetaryControls.autoRotateSpeed = 2.0;
                this.classList.toggle('active');
                statusMessage.textContent = planetaryControls.autoRotate ? 'Planetary auto-rotation enabled' : 'Planetary auto-rotation disabled';
                setTimeout(() => {
                    statusMessage.textContent = 'Advanced Simulation Ready';
                }, 2000);
            });
            
            zoomViewBtn.addEventListener('click', function() {
                planetaryCamera.position.set(0, 0, 5);
                planetaryControls.update();
                statusMessage.textContent = 'Zoomed to planet';
                setTimeout(() => {
                    statusMessage.textContent = 'Advanced Simulation Ready';
                }, 2000);
            });
            
            // Custom planet gravity adjustment
            document.querySelector('.planet-card[data-planet="custom"]').addEventListener('click', function() {
                const customGravity = prompt('Enter custom gravity (m/s²):', '9.81');
                if (customGravity !== null && !isNaN(customGravity) && customGravity > 0) {
                    planets.custom.gravity = parseFloat(customGravity);
                    planets.custom.diameter = 'Variable';
                    updatePlanetInfo(planets.custom);
                    statusMessage.textContent = `Custom gravity set to ${customGravity} m/s²`;
                    setTimeout(() => {
                        statusMessage.textContent = 'Advanced Simulation Ready';
                    }, 2000);
                }
            });
        }
        
        // ====== INITIALIZATION ======
        function init() {
            // Initialize Three.js scenes
            initThreeJS();
            initPlanetaryViewer();
            initFullscreenScene();
            
            // Initialize Chart.js
            initChartJS();
            
            // Set up event listeners
            setupEventListeners();
            
            // Update UI with initial values
            updateControlValues();
            updateAdvancedControls();
            updateWindControls();
            updateEnvironmentalControls();
            updateProjectileCustomization();
            updateViewControls();
            
            // Calculate initial theoretical values
            const angleRad = launchAngle * Math.PI / 180;
            const theoreticalMaxRange = (initialVelocity * initialVelocity * Math.sin(2 * angleRad)) / gravity;
            maxRangeEl.textContent = theoreticalMaxRange.toFixed(1) + " m";
            
            // Initial planetary calculation
            calculateAllPlanets();
            
            // Initialize history
            updateHistoryList();
            
            console.log('SIT Advanced Projectile Motion Simulator v4.0 initialized successfully!');
            statusMessage.textContent = 'Advanced Simulation Ready';
        }
        
        // Start the application when the page loads
        window.addEventListener('load', init);
    </script>
</body>
</html>

