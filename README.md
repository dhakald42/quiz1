<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>H.D. Engineering - Professional Assessment Dashboard</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap');

        :root {
            --sidebar-bg: #0f172a;
            --sidebar-text: #cbd5e1;
            --bg-color: #f8fafc;
            --card-bg: #ffffff;
            --primary: #2563eb;
            --primary-hover: #1d4ed8;
            --text-main: #1e293b;
            --text-muted: #64748b;
            --border: #e2e8f0;
            --success: #10b981;
            --danger: #ef4444;
            --warning: #f59e0b;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Inter', sans-serif; }

        body { display: flex; height: 100vh; background-color: var(--bg-color); color: var(--text-main); overflow: hidden; }

        /* Sidebar & Navigator */
        .sidebar { width: 320px; background-color: var(--sidebar-bg); color: white; display: flex; flex-direction: column; height: 100%; box-shadow: 4px 0 15px rgba(0,0,0,0.1); z-index: 10; }
        .brand { padding: 25px; border-bottom: 1px solid rgba(255,255,255,0.1); text-align: center; }
        .brand h2 { font-size: 1.2rem; font-weight: 700; letter-spacing: 0.5px; color: #ffffff; }
        .brand p { font-size: 0.8rem; color: var(--sidebar-text); margin-top: 5px; text-transform: uppercase; letter-spacing: 1px; }
        
        .nav-header { padding: 20px 25px 10px; font-size: 0.85rem; font-weight: 600; color: var(--sidebar-text); text-transform: uppercase; }
        .question-grid { display: grid; grid-template-columns: repeat(5, 1fr); gap: 8px; padding: 0 25px 25px; overflow-y: auto; align-content: start; }
        
        .q-node { 
            aspect-ratio: 1; border-radius: 6px; background: rgba(255,255,255,0.1); border: 1px solid rgba(255,255,255,0.05);
            display: flex; align-items: center; justify-content: center; font-size: 0.85rem; font-weight: 600; cursor: pointer; transition: 0.2s;
        }
        .q-node:hover:not(.locked) { background: rgba(255,255,255,0.2); }
        .q-node.active { border: 2px solid var(--primary); background: var(--primary); }
        .q-node.answered-correct { background: var(--success); border-color: var(--success); }
        .q-node.answered-wrong { background: var(--danger); border-color: var(--danger); }
        
        /* Locked Node Styling */
        .q-node.locked { opacity: 0.3; cursor: not-allowed; border: 1px dashed rgba(255,255,255,0.2); background: rgba(0,0,0,0.2); }

        /* Main Content */
        .main-content { flex: 1; display: flex; flex-direction: column; height: 100%; overflow-y: auto; }
        
        /* Top Header */
        .top-header { display: flex; justify-content: space-between; align-items: center; padding: 20px 40px; background: var(--card-bg); border-bottom: 1px solid var(--border); }
        .header-title h1 { font-size: 1.5rem; color: var(--text-main); }
        .header-title p { font-size: 0.9rem; color: var(--text-muted); }
        .user-profile { display: flex; align-items: center; gap: 15px; }
        .avatar { width: 40px; height: 40px; border-radius: 50%; background: var(--primary); color: white; display: flex; align-items: center; justify-content: center; font-weight: 700; font-size: 1.2rem; }
        .user-info h4 { font-size: 0.95rem; }
        .user-info p { font-size: 0.8rem; color: var(--text-muted); }

        /* Dashboard Stats */
        .stats-container { display: flex; gap: 20px; padding: 30px 40px 10px; }
        .stat-card { flex: 1; background: var(--card-bg); padding: 20px; border-radius: 12px; border: 1px solid var(--border); display: flex; align-items: center; gap: 20px; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.02); }
        .stat-icon { width: 50px; height: 50px; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: 1.5rem; }
        .icon-blue { background: #eff6ff; color: var(--primary); }
        .icon-green { background: #f0fdf4; color: var(--success); }
        .icon-orange { background: #fffbeb; color: var(--warning); }
        .stat-data h3 { font-size: 1.8rem; color: var(--text-main); margin-bottom: 2px; }
        .stat-data p { font-size: 0.85rem; color: var(--text-muted); font-weight: 600; text-transform: uppercase; }

        /* Quiz Area */
        .quiz-container { padding: 20px 40px 40px; max-width: 1000px; margin: 0 auto; width: 100%; }
        .quiz-card { background: var(--card-bg); border-radius: 16px; border: 1px solid var(--border); padding: 40px; box-shadow: 0 10px 15px -3px rgba(0,0,0,0.05); }
        
        .q-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 25px; padding-bottom: 20px; border-bottom: 1px solid var(--border); }
        .q-badge { background: #eff6ff; color: var(--primary); padding: 6px 14px; border-radius: 20px; font-weight: 700; font-size: 0.9rem; }
        
        .question-text { font-size: 1.3rem; font-weight: 600; line-height: 1.5; color: var(--text-main); margin-bottom: 30px; }
        
        .options-grid { display: flex; flex-direction: column; gap: 15px; }
        .option { 
            display: flex; align-items: center; padding: 18px 25px; border: 2px solid var(--border); border-radius: 12px; 
            cursor: pointer; transition: all 0.2s; font-size: 1.05rem; background: #fafafa; font-weight: 500;
        }
        .option:hover:not(.disabled) { border-color: var(--primary); background: #eff6ff; }
        .option .opt-letter { width: 30px; height: 30px; border-radius: 6px; background: #e2e8f0; display: flex; align-items: center; justify-content: center; margin-right: 15px; font-weight: 700; font-size: 0.9rem; color: var(--text-muted); transition: 0.2s; }
        
        /* Answer States */
        .option.disabled { pointer-events: none; opacity: 0.7; }
        .option.correct { background: #f0fdf4; border-color: var(--success); opacity: 1; }
        .option.correct .opt-letter { background: var(--success); color: white; }
        .option.wrong { background: #fef2f2; border-color: var(--danger); opacity: 1; }
        .option.wrong .opt-letter { background: var(--danger); color: white; }

        .actions { display: flex; justify-content: space-between; margin-top: 30px; padding-top: 20px; border-top: 1px solid var(--border); }
        .btn { padding: 12px 25px; border-radius: 8px; font-weight: 600; font-size: 1rem; cursor: pointer; transition: 0.2s; border: none; display: flex; align-items: center; gap: 10px; }
        .btn-outline { background: transparent; border: 2px solid var(--border); color: var(--text-main); }
        .btn-outline:hover:not(:disabled) { background: #f1f5f9; }
        .btn-primary { background: var(--primary); color: white; }
        .btn-primary:hover:not(:disabled) { background: var(--primary-hover); }
        
        /* Disabled Button Styling */
        .btn:disabled { opacity: 0.5; cursor: not-allowed; }

        /* Custom Scrollbar for Sidebar */
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.2); border-radius: 10px; }
    </style>
</head>
<body>

    <aside class="sidebar">
        <div class="brand">
            <h2>H.D. ENGINEERING</h2>
            <p>Solution Pvt. Ltd.</p>
        </div>
        <div class="nav-header">Question Navigator (<span id="nav-completed">0</span>/100)</div>
        <div class="question-grid" id="q-grid">
            </div>
    </aside>

    <main class="main-content">
        <header class="top-header">
            <div class="header-title">
                <h1>Civil Engineering Assessment</h1>
                <p>Professional Certification Module • Set 1 (Strict Mode)</p>
            </div>
            <div class="user-profile">
                <div class="user-info" style="text-align: right;">
                    <h4>Deepak Dhakal</h4>
                    <p>Owner</p>
                </div>
                <div class="avatar">DD</div>
            </div>
        </header>

        <div class="stats-container">
            <div class="stat-card">
                <div class="stat-icon icon-blue"><i class="fas fa-tasks"></i></div>
                <div class="stat-data">
                    <h3 id="stat-attempted">0</h3>
                    <p>Attempted</p>
                </div>
            </div>
            <div class="stat-card">
                <div class="stat-icon icon-green"><i class="fas fa-check-circle"></i></div>
                <div class="stat-data">
                    <h3 id="stat-score">0</h3>
                    <p>Correct Answers</p>
                </div>
            </div>
            <div class="stat-card">
                <div class="stat-icon icon-orange"><i class="fas fa-bullseye"></i></div>
                <div class="stat-data">
                    <h3 id="stat-accuracy">0%</h3>
                    <p>Accuracy</p>
                </div>
            </div>
        </div>

        <div class="quiz-container">
            <div class="quiz-card">
                <div class="q-header">
                    <div class="q-badge" id="q-badge">Question 1 of 100</div>
                    <div style="color: var(--text-muted); font-weight: 600;"><i class="fas fa-lock"></i> Sequential Mode</div>
                </div>
                
                <h2 class="question-text" id="question-text">Loading question...</h2>
                
                <div class="options-grid" id="options-container">
                    </div>

                <div class="actions">
                    <button class="btn btn-outline" id="btn-prev" onclick="navigate(-1)"><i class="fas fa-arrow-left"></i> Previous</button>
                    <button class="btn btn-primary" id="btn-next" onclick="navigate(1)" disabled>Next Question <i class="fas fa-arrow-right"></i></button>
                </div>
            </div>
        </div>
    </main>

<script>
    // The Complete 100 Questions Array
    const questions = [
        { text: "Most reliable power is", options: ["Hydroelectric power", "Combined system and hydroelectric power", "Wind power", "Diesel powered"], answer: 1 },
        { text: "\"Cold frontal precipitation\" is", options: ["Small catchment area with heavy precipitation", "Small catchment area with moderate precipitation", "Large catchment area with heavy precipitation", "Large catchment area with moderate precipitation"], answer: 0 },
        { text: "Number of professional engineers registered by the time 2078/12/31", options: ["71", "61", "51", "41"], answer: 0 },
        { text: "Expected turn out of cement concrete 1:2:4 used per mason per day will be", options: ["1.5 m³", "2.5 m³", "3.5 m³", "5.0 m³"], answer: 0 },
        { text: "A simply supported beam having concentrated load at midpoint, the bending moment diagram is", options: ["rectangle", "triangle", "parabolic", "semi-circle"], answer: 1 },
        { text: "The deflection of simply supported beam at mid-point with point load 'w' at its center is", options: ["WL³/3EI", "WL³/12EI", "WL³/48EI", "WL³/384EI"], answer: 2 },
        { text: "The overlap for a weld connection in battens should not be less than", options: ["3t", "4t", "6t", "8t"], answer: 1 },
        { text: "The soil which has been acted upon by stress greater than present stress is", options: ["Under consolidated", "Over consolidated", "Normal consolidated", "Pre consolidated"], answer: 1 },
        { text: "Which of the following drainage is not used in tunnel?", options: ["Fore drainage", "Tunnel drainage", "Permanent drainage", "Side drainage"], answer: 0 },
        { text: "When the canal bed is taken over drainage?", options: ["Siphon", "Aqueduct", "Super passage", "Cross drainage"], answer: 1 },
        { text: "The drainage provided to escape water from water logged area is", options: ["Surface drainage", "Subsurface drainage", "Deep drainage", "All of the above"], answer: 3 },
        { text: "A 180-degree elbow pipe of diameter 180mm discharging water at 0.15 m³/s Inlet and outlet pressure are 200Kpa and 195Kpa. The force exerted by water at the bend is", options: ["127N", "128N", "125N", "127.8N"], answer: 1 },
        { text: "A nozzle discharging water into the stationary plate, the force exerted on plate is", options: ["ρ * A * Q", "ρ * A * V", "ρ * V²", "ρ * V * Q"], answer: 3 },
        { text: "The maximum vertical clearance in hill road for overhanging cliff is", options: ["3m", "4m", "5m", "6m"], answer: 2 },
        { text: "The sieve size used in Los Angle Abrasion test is", options: ["1.7 mm", "2.36 mm", "1.77 mm", "1.5 mm"], answer: 0 },
        { text: "Which foundation is constructed in group", options: ["pile", "pier", "well", "caisson"], answer: 0 },
        { text: "The pH value of fresh sewage is usually", options: ["Equal to 7", "More than 7", "Less than 7", "Equal to zero"], answer: 1 },
        { text: "Fitting used to connect pipes of different diameter is", options: ["union", "nipple", "reducer coupling", "spigot"], answer: 2 },
        { text: "Scour valve is provided at", options: ["At the high points", "At the change of direction of pipe", "At the low points", "At the junction of pipes"], answer: 2 },
        { text: "ILD for Shear Force at the support of cantilever beam is", options: ["Triangle between free end and fixed end", "Triangle between free end and section", "Rectangle between free end and fixed end", "Rectangle between free end and section"], answer: 2 },
        { text: "E-Coli dies when PH of water is", options: ["7", "6.5", "8.5", "9.5"], answer: 3 },
        { text: "The pressure energy of water is converted to kinetic energy through nozzle provided next to runner blade in", options: ["Impulse turbine", "Reaction turbine", "Both (a) and (b)", "Kaplan turbine"], answer: 0 },
        { text: "Which of the following is not deep foundations:", options: ["Caisson", "Mat foundation", "End-bearing pile", "Friction pile"], answer: 1 },
        { text: "Specific volume is defined as reciprocal of", options: ["density", "specific gravity", "relative density", "mass"], answer: 0 },
        { text: "\"Aggrading channel\" is", options: ["silting", "scouring", "regime", "meandering"], answer: 0 },
        { text: "If an engineer violates NEC act, from, subsection 30(b) of Nepal Engineering Council Act 2055 the punishment is", options: ["NRs 2000 fine", "NRs 3000 fine", "NRs 10000 fine", "NRs 25000 fine"], answer: 1 },
        { text: "Nepal Engineer’s Association is", options: ["Government body to support NEC", "Independent organization of Nepalese engineers", "Private body which works for betterment of Engineers", "Non-government organization"], answer: 1 },
        { text: "EPC stands for", options: ["Engineering, Procurement and Construction", "Engineering, Procurement and Contract", "Economic, Profit and Cost", "All of the above"], answer: 0 },
        { text: "The dowel bars are provided to", options: ["Resist shear stress", "Resist bending stress", "Resist frictional stress", "Transfer stress from one to another"], answer: 3 },
        { text: "The gauge pressure at the depth of 1m inside the water is", options: ["9810 MPa", "9810KPa", "9810Pa", "1Bar"], answer: 2 },
        { text: "The perpendicular axis theorem is used to calculate the Moment of Inertia about", options: ["Axis perpendicular to plane", "Axis parallel to plane", "Axis through centroid", "Both (a) and (b)"], answer: 0 },
        { text: "The angle subtended by rigid cone below foundation with respect to horizontal is", options: ["Φ", "45°", "90°-Φ/2", "45°+Φ/2"], answer: 3 },
        { text: "Three pipes are connected in series then", options: ["Head loss is same for all", "Discharge is same for all", "Friction factor is same for all", "Velocity is same for all"], answer: 1 },
        { text: "A circular shaft is acted by a torsion, the stress at the center of shaft is", options: ["Zero", "Maximum", "Minimum", "Infinite"], answer: 0 },
        { text: "For a simply supported beam, a neutral axis is", options: ["Where stress is zero", "Where stress is maximum", "Bending moment zero and shear force maximum", "Bending moment maximum and shear force zero"], answer: 0 },
        { text: "For a simply supported beam of effective span 10m, the normal span to depth ratio is", options: ["6", "20", "26", "30"], answer: 1 },
        { text: "In slope deflection method, the slope and deflection of joints are computed using", options: ["Rigidity of joint", "Elasticity of joint", "Equilibrium of structure", "Equilibrium of joint"], answer: 3 },
        { text: "Earthquake resistant design for low strength masonry is from building code", options: ["NBC-202", "NBC-203", "NBC-204", "NBC-205"], answer: 1 },
        { text: "The law that governs the standards of construction of structural and non-structural buildings in Nepal", options: ["Building by laws", "Building Code", "Environment regulation act", "None of the above"], answer: 1 },
        { text: "The livestock demand should not exceed .... of the total domestic demand", options: ["20%", "30%", "40%", "50%"], answer: 0 },
        { text: "The process of determining fair price, or value is", options: ["Depreciation", "Inflation", "Valuation", "All of the above"], answer: 2 },
        { text: "Double mass curve technique is used for checking", options: ["Consistency of a record", "Inconsistency of a record", "Both (a) and (b)", "None of these"], answer: 0 },
        { text: "The capacity of irrigation canal is determined by", options: ["Kor water depth (Rabi)", "Kor water depth (Khariff)", "Average irrigation depth (Rabi)", "Average irrigation depth (Khariff)"], answer: 1 },
        { text: "The head loss is minimum in", options: ["Broad crested weir", "Narrow crested weir", "Oggee shaped weir", "Sharp crested weir"], answer: 2 },
        { text: "Completion of an activity on CPM network diagram, is generally known", options: ["Event", "Node", "Connector", "All"], answer: 0 },
        { text: "The weir constructed such that the weight of structure is completely balanced by upward seepage force of water?", options: ["Gravity weir", "Non-gravity weir", "Glacis weir", "None of the above"], answer: 3 },
        { text: "In traffic engineering, clearance time is indicated by", options: ["Red", "Green", "Amber", "White"], answer: 2 },
        { text: "The bitumen layer spread over old pavement to bind newer pavement together is", options: ["Seal coat", "Prime coat", "Tack coat", "All of the above"], answer: 2 },
        { text: "The term (i+1)⁻ⁿ is", options: ["Single payment discount amount factor", "Single payment compound amount factor", "Both a and b", "All"], answer: 0 },
        { text: "Which of following contour is a contour index? 97,98,101,105,107", options: ["97", "105", "101", "107"], answer: 1 },
        { text: "The permissible stress to which a structural member can be subjected to, is known as", options: ["bearing stress", "working stress", "tensile stress", "compressive stress"], answer: 1 },
        { text: "The shape factor of standard rolled beam section varies from", options: ["1.10 to 1.20", "1.20 to 1.30", "1.30 to 1.40", "1.40 to 1.50"], answer: 0 },
        { text: "The equivalent length is of a column of length L having both the ends fixed, is", options: ["2L", "L", "L/2", "L/12"], answer: 2 },
        { text: "Isohyets are the imaginary lines joining the points of equal", options: ["Pressure", "Height", "Humidity", "Rainfall"], answer: 3 },
        { text: "The pressure at a point in a fluid will not be same in all directions when the fluid is", options: ["Moving", "Viscous", "Viscous and static", "Viscous and moving"], answer: 3 },
        { text: "The maximum water content at which a reduction in water content doesn't cause a decrease in volume of a soil mass, is known as", options: ["Liquid limit", "Plastic limit", "Shrinkage limit", "Permeability limit"], answer: 2 },
        { text: "The radius of curvature of the arc of the bubble tube is generally kept", options: ["10 m", "25 m", "50 m", "100 m"], answer: 0 },
        { text: "The coefficient of curvature for a well graded soil must be between", options: ["0.5 to 1", "1.0 to 3.0", "3.0 to 4.0", "4.0 to 5.0"], answer: 1 },
        { text: "The out turn of brickwork with cement mortar in foundation is", options: ["1.25 m³", "2.5 m³", "3.5 m³", "4.5 m³"], answer: 0 },
        { text: "A body is subjected to a direct tensile stress of 300 MPa in one plane accompanied by a simple shear stress of 200 MPa. The maximum normal stress on the plane will be", options: ["300 MPa", "350 MPa", "400 MPa", "450 MPa"], answer: 2 },
        { text: "Void ratio of a soil is 0.68 and specific gravity is 2.68. The critical gradient for quick sand condition is", options: ["1.5", "1.0", "2.0", "0"], answer: 1 },
        { text: "The readings taken during traversing using total station are", options: ["Horizontal angle, Vertical angle, Horizontal distance, vertical distance", "Horizontal angle, Vertical angle, Horizontal distance, vertical distance, station height", "Horizontal angle, Vertical angle, Horizontal distance, station height", "Horizontal angle, Horizontal distance, vertical distance, station height, height of instrument"], answer: 3 },
        { text: "In a three hinged arch with supports at different height h1 and h2, load W is udl acting at the whole span of arch. If L is the length of the span, find the horizontal thrust.", options: ["WL / (h1²+h2²)", "WL² / 2(√h1+√h2)²", "WL / 2(h1+h2)²", "WL / 4(h1²+h2²)"], answer: 1 },
        { text: "A cantilever beam 2m long is subjected to a point load of 2.4kN at its free end. The size of the beam is 40mm x 60mm. Find the stress during collapse of beam.", options: ["180 N/mm²", "200 N/mm²", "160 N/mm²", "220 N/mm²"], answer: 1 },
        { text: "Water is flowing at 1 m/sec through a pipe of 10cm diameter with a right-angle bend. The force in Newton exerted on the bend by water is", options: ["(5/2)π²", "(2/5)", "(2/5π)", "5π/2"], answer: 3 },
        { text: "The force exerted by a jet of water on a stationary vertical plate in the direction of jet is", options: ["ρav", "ρav²", "ρav²/2", "none"], answer: 1 },
        { text: "The influence line diagram for BM at a section in a cantilever will be a triangle extending between the section and the", options: ["fixed end with maximum ordinate under the section", "fixed end with maximum ordinate under the fixed end", "unsupported end with maximum ordinate at the section", "unsupported end with maximum ordinate at the unsupported end"], answer: 0 },
        { text: "What will be the sludge volume Index (SVI) if 100 ml of sludge collected in 30 minutes on drying weighs 800mg?", options: ["115", "78", "125", "100"], answer: 2 },
        { text: "If the subgrade soil has a bearing load 1370Kg at 2.5 mm penetration, the load sustained by soil is 64.6kg. what will be the CBR value?", options: ["10.3%", "4.71%", "6.67%", "5.4%"], answer: 1 },
        { text: "The tractive force ratio for regime canal with angle of repose 36° and side slope 30° is", options: ["0.48", "0.69", "0.35", "0.53"], answer: 0 },
        { text: "In a wide rectangular channel, the normal depth is increased by 20%. The discharge in the channel would increase by", options: ["20%", "26%", "36%", "56%"], answer: 2 },
        { text: "A moist soil sample weighing 108 g has a volume of 60 cc. If water content is 25% and value of G = 2.52, the void ratio is", options: ["0.55", "0.65", "0.75", "0.80"], answer: 2 },
        { text: "The bearings of the lines AB and BC are 146°30' and 68°30'. The included angle ABC is", options: ["102°", "78°", "45°", "None of these"], answer: 0 },
        { text: "Water flows through a circular tube with a velocity of 2 m/s. The diameter of the pipe is 14 cm. Take kinematic viscosity of water 10⁻⁶ m²/s and density of water 1000 kg/m³", options: ["2.8 x 10⁸", "2.8 x 10⁵", "2800", "28000"], answer: 1 },
        { text: "A body is subjected to a tensile stress of 1200 MPa on one plane and another tensile stress of 600 MPa on a plane at right angles to the former. It is also subjected to a shear stress of 400 MPa on the same planes. The maximum normal stress and shear stress will be", options: ["400 Mpa, 500Mpa", "500 Mpa, 1400Mpa", "900 Mpa, 1400Mpa", "1400 Mpa, 500Mpa"], answer: 3 },
        { text: "If the effective working time is 7 hours and per batch time of concrete is 3 minutes, the output of a concrete mixer of 150-liter capacity is", options: ["15,900 liters", "16,900 liters", "17,900 liters", "18,900 liters"], answer: 3 },
        { text: "In slow sand filters, the turbidity of raw water can be removed only up to", options: ["60 mg/l", "75 mg/l", "100 mg/l", "150 mg/l"], answer: 0 },
        { text: "If the Irrigation Efficiency is 80%, conveyance losses are 20% and the actual depth of watering is 16 cm, the depth of water required at the canal outlet, is", options: ["10 cm", "15 cm", "20 cm", "25 cm"], answer: 2 },
        { text: "If the coefficient of friction on the road surface is 0.15 and a maximum super-elevation 1 in 15 is provided, the maximum speed of the vehicles on a curve of 100-meter radius, is", options: ["32.44 km/hour", "42.44 km/hour", "52.44 km/hour", "62.44 km/hour"], answer: 2 },
        { text: "The expected time for PERT event will be... If pessimistic time, optimistic time and most likely time is 10 days, 2 days and 3 days", options: ["3 days", "4 days", "5 days", "6 days"], answer: 1 },
        { text: "The ratio of COD to BOD of untreated sewage is", options: ["1.25-1.5", "2.5-5", "5-10", "None"], answer: 1 },
        { text: "S/d of solid circular timber column should not exceed", options: ["25", "50", "75", "100"], answer: 1 },
        { text: "Center of gravity of hemisphere is", options: ["3r/8", "4r/5π", "3r/4", "4r/3π"], answer: 0 },
        { text: "Froude number of triangular channel of depth y with side slope 2H:1V is", options: ["V/√(g*y/4)", "V/√(g*y/2)", "V/√(g*y)", "V/√(g*y/3)"], answer: 1 },
        { text: "Which of the following statement is correct about stream length of fan shaped and fern shaped catchment?", options: ["stream length of fan shaped catchment is more", "stream length of fern shaped catchment is more", "stream length of both catchment is same", "None of these"], answer: 1 },
        { text: "Blue baby syndrome is caused due to", options: ["Nitrate", "Nitrite", "Chloride", "Sulphate"], answer: 0 },
        { text: "A train of wheel load is moving over a simply supported beam. The shear force will be maximum at support A when", options: ["Trail load is at A", "Leading load is at A", "Load is at mid span", "Shear force will be equal in all cases"], answer: 0 },
        { text: "Gross area of footing is dependent on", options: ["Load from superstructure", "Bearing capacity of soil", "Type of soil", "All of these"], answer: 3 },
        { text: "The depth of exploration is independent of", options: ["Size of structure", "Type of structure", "type of boring", "Load of structure"], answer: 2 },
        { text: "In a simply supported beam, two equal point loads P are acting at a distance of L/3 from both supports A and B. what is the value of shear force at distance L/6 from support A?", options: ["P", "2P", "P/3", "P/6"], answer: 0 },
        { text: "What is the value of pressure inside a soap bubble having radius R?", options: ["8T/R", "4T/R", "2T/R", "T/R"], answer: 1 },
        { text: "The flow duration curve at a given head of a hydroelectric plant is used to determine", options: ["total power available at the site.", "total units of energy available.", "load-factor at the plant.", "diversity factor for the plant."], answer: 0 },
        { text: "What is the standard temperature for Marshall Stability test?", options: ["27°C", "25°C", "60°C", "40°C"], answer: 2 },
        { text: "Due to lined channel section", options: ["Water logging increases", "cross sectional area increases", "command area increases", "All of these"], answer: 2 },
        { text: "Water is flowing down steadily in a constant cross sectional pipe. According to Bernoulli principle", options: ["Pressure increases with height", "Pressure decreases with height", "Velocity increases with height", "None of these"], answer: 1 },
        { text: "A solid having equilateral triangle as base and other faces converging towards its axis is", options: ["Prism", "Pyramid", "Hexahedron", "cone"], answer: 1 },
        { text: "Which method gives accurate flow characteristics in context of river of Nepal?", options: ["WECS/DHM 1990", "MIP", "DHM 2004", "None of these"], answer: 0 },
        { text: "Lacquer is", options: ["Oil paints", "Distemper", "Spirit Varnish", "Enamel paints"], answer: 2 },
        { text: "Which method of project planning was invented first?", options: ["Bar chart", "Milestone chart", "CPM", "PERT"], answer: 0 },
        { text: "Which Engineer is registered in category A?", options: ["General Engineer", "Professional Engineer", "Foreigner Engineer", "All of them"], answer: 1 }
    ];

    // Dashboard State
    let currentQ = 0;
    let maxUnlockedQ = 0; // Tracks the highest question the user is allowed to view
    let userAnswers = new Array(100).fill(null);
    let stats = { attempted: 0, correct: 0 };
    const letters = ['A', 'B', 'C', 'D'];

    // Initialize Dashboard
    window.onload = () => {
        buildGrid();
        loadQuestion(0);
    };

    // Build Sidebar Grid
    function buildGrid() {
        const grid = document.getElementById('q-grid');
        grid.innerHTML = '';
        for (let i = 0; i < 100; i++) {
            const node = document.createElement('div');
            node.className = 'q-node';
            if (i > maxUnlockedQ) node.classList.add('locked'); // Lock future questions
            node.id = `node-${i}`;
            node.innerText = i + 1;
            node.onclick = () => {
                // Only allow clicking if the question is unlocked
                if (i <= maxUnlockedQ) {
                    loadQuestion(i);
                }
            };
            grid.appendChild(node);
        }
    }

    // Load Specific Question
    function loadQuestion(index) {
        if(index < 0 || index >= 100) return;
        currentQ = index;
        
        // Update Grid UI to reflect active, locked, and unlocked states
        document.querySelectorAll('.q-node').forEach((n, idx) => {
            n.classList.remove('active');
            if (idx > maxUnlockedQ) {
                n.classList.add('locked');
            } else {
                n.classList.remove('locked');
            }
        });
        
        document.getElementById(`node-${index}`).classList.add('active');
        
        // Ensure node is visible in sidebar scroll
        document.getElementById(`node-${index}`).scrollIntoView({ behavior: "smooth", block: "nearest" });

        // Build Question UI
        const q = questions[currentQ];
        document.getElementById('q-badge').innerText = `Question ${currentQ + 1} of 100`;
        document.getElementById('question-text').innerText = `${currentQ + 1}. ${q.text}`;
        
        const container = document.getElementById('options-container');
        container.innerHTML = '';

        q.options.forEach((opt, i) => {
            const div = document.createElement('div');
            div.className = 'option';
            div.innerHTML = `<div class="opt-letter">${letters[i]}</div><div>${opt}</div>`;
            
            // Handle Previously Answered State
            if (userAnswers[currentQ] !== null) {
                div.classList.add('disabled');
                if (i === q.answer) div.classList.add('correct');
                else if (i === userAnswers[currentQ]) div.classList.add('wrong');
            } else {
                div.onclick = () => submitAnswer(i, div);
            }
            
            container.appendChild(div);
        });

        // Button States
        document.getElementById('btn-prev').style.visibility = currentQ === 0 ? 'hidden' : 'visible';
        
        const btnNext = document.getElementById('btn-next');
        btnNext.innerHTML = currentQ === 99 ? 'Finish <i class="fas fa-flag-checkered"></i>' : 'Next Question <i class="fas fa-arrow-right"></i>';
        
        // Disable 'Next' button if the current question hasn't been answered yet
        if (userAnswers[currentQ] === null) {
            btnNext.disabled = true;
        } else {
            btnNext.disabled = false;
        }
    }

    // Process Answer Selection
    function submitAnswer(selectedIndex, el) {
        const q = questions[currentQ];
        userAnswers[currentQ] = selectedIndex;
        
        const isCorrect = (selectedIndex === q.answer);
        
        // Update Stats
        stats.attempted++;
        if (isCorrect) stats.correct++;
        
        document.getElementById('stat-attempted').innerText = stats.attempted;
        document.getElementById('nav-completed').innerText = stats.attempted;
        document.getElementById('stat-score').innerText = stats.correct;
        
        const acc = Math.round((stats.correct / stats.attempted) * 100);
        document.getElementById('stat-accuracy').innerText = acc + '%';

        // Update UI for the options
        const allOptions = document.querySelectorAll('.option');
        allOptions.forEach((opt, idx) => {
            opt.classList.add('disabled');
            if (idx === q.answer) opt.classList.add('correct');
        });
        
        if (!isCorrect) {
            el.classList.add('wrong');
        }

        // Update Grid Node Color
        const node = document.getElementById(`node-${currentQ}`);
        node.classList.add(isCorrect ? 'answered-correct' : 'answered-wrong');
        
        // --- STRICT MODE LOGIC ---
        // Unlock the next question in the sequence
        if (currentQ === maxUnlockedQ && maxUnlockedQ < 99) {
            maxUnlockedQ++;
            document.getElementById(`node-${maxUnlockedQ}`).classList.remove('locked');
        }
        
        // Re-enable the next button so the user can proceed
        document.getElementById('btn-next').disabled = false;
    }

    // Prev/Next Navigation
    function navigate(dir) {
        if (currentQ + dir < 100) {
            loadQuestion(currentQ + dir);
        } else if (currentQ + dir === 100) {
            alert(`Assessment Complete!\nFinal Score: ${stats.correct} out of 100\nAccuracy: ${document.getElementById('stat-accuracy').innerText}`);
        }
    }
</script>

</body>
</html>
