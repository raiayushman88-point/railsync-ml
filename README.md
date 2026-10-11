The problem RailSync solves
🚂 RailSync – COORDINATED,OPTIMIZED,AI-DRIVEN BLOCK PLANNER
Background:
Railway maintenance for fixed infrastructure of Engineering, Traction Distribution, and Signal & Telecommunication departments is currently planned independently.
Each department requests maintenance blocks/disconnections via the BDMS system. This planning process is decentralized and manual. This often leads to inefficient
block utilization, poor coordination, and suboptimal scheduling, which may reduce asset availability and impact train operations.Maintenance data such as defects 
and overdue tasks is maintained separately in systems like Track Management System (TMS), Signalling Maintenance & Management System (SMMS), and Traction
Distribution Management System (TDMS). Meanwhile, the Control Office Application (COA) manages block corridor availability. Without integration and coordinated
scheduling, maintenance blocks/disconnections are not optimally planned, resulting in asset downtime and reduced availability of fixedinfrastructure for train 
operation.

Our Solution:
We developed RailSync – An Automatic Block Planning System that integrates maintenance, defects and corridor data to generate optimized block schedules. 
The system prioritizes maintenance activities to minimize asset downtime and maximize the availability of critical infrastructure, ensuring uninterrupted train
operations.

As per Expected Solution, we built an AI system that includes:

1.Integration: Integration of maintenance data (defects, overdue maintenance) from TMS, SMMS, and TDMS with corridor block and block availability as per the Train 
Time Table and the goods trains forecast from the Control Office. Implemented via live interactive corridor map with real-time train simulation.

2.AI/ML Prioritization: Uses AI/ML algorithms to prioritize and schedule maintenance tasks based on criticality, urgency, and impact on asset availability.

javascriptPriority Score = Safety Risk×0.25 + Criticality×0.20 + Operational Impact×0.20 + Severity×0.15 + Urgency×0.10 + Overdue×0.103.

3.Optimized Scheduling: Optimize block scheduling to maximize asset uptime by minimizing downtime and efficiently coordinating multi-department activities using 
Google OR-Tools CP-SAT Solver. Achieved 11/11 requests scheduled with 99.75% track availability. Includes heuristic PAUT fallback so planning never fails.

4.Multi-Horizon Planning: Provides block plans over multiple time horizons - weekly and monthly - to support both short-term and long-term maintenance with 
Calendar View, Corridor Traffic Forecast Heatmap, and Bulk Approval.
Our 4-Dashboard Ecosystem:

5.Maintenance Team: Logs defects and requests track blocks. Unified request, multi-corridor support (Raipur-Durg), downloadable defect register, dynamic status 
window.

6.AI Engine (The Brain): Scores priority, builds optimized Weekly/Monthly plans using OR-Tools, coordinated scheduling with live timetable, calendar view.

7.Control Center: Approves/modifies blocks using live timetable and interactive map. 3 AI options (As Requested, Conflict-Avoiding Shift, Shortened Duration), 
Bulk Section Approval, GeoTag verification, Horizon Plan Review, Traffic Forecast.

8.Field Crew: Executes work and submits geo-tagged Before/After proof with Use My Location, work description, time variance tracking. Actual runtimes feed back to
retrain AI for higher accuracy.

Maintenance Team ──► AI Engine ──► Control Center ──► Field Crew
▲ │
└────────── actual runtimes retrain the model ◄──────┘

This transforms current decentralized and manual block planning into a data-driven, coordinated process that maximizes asset availability, improves safety, and 
supports reliable train operations.

Live: https://railsync-red.vercel.app | API: https://railsync-ml.onrender.com

Challenges we ran into
Building RailSync was not straightforward - we were integrating 4 different railway systems into one.

1. Google OR-Tools Integration with Python Backend:
Our biggest hurdle. OR-Tools CP-SAT is not a simple library - defining interval variables, no-overlap constraints on same segment, and maximizing availability was
complex. Initial model was infeasible for >10 requests and returned UNSAT. We fixed it by adding a Heuristic Fallback (PAUT time-window logic) so if CP-SAT fails,
the system still generates a plan. Now it achieves 11/11 scheduled with 99.75% availability.

2. Render Free Tier Sleep Issue:
Our ML Backend on Render (railsync-ml.onrender.com) used to sleep after 15 mins, causing 50+ sec cold start and frontend timeouts. Judges thought the site was
broken. We solved it by creating a cron-job that pings the /health endpoint every 10 seconds to keep it always warm.

3. Real-time Corridor Map & Train Simulation:
Building a live Raipur-Durg corridor map with moving trains, live speed, and Clear/Occupied status was tough with Leaflet. Train positions were jittering. We built
a custom simulation engine that interpolates train movement between stations based on timetable and goods train forecast, plus switchable views
(Interactive Map / Track Schematic).

4. Supabase Real-time Sync Across 4 Dashboards:
When Maintenance team creates a request, AI dashboard should instantly see it, Control should get it, Field should get approved jobs. We struggled with real-time
subscriptions and RLS policies. Fixed by using Supabase Realtime channels with proper row-level security.

5. Geo-tagged Verification Flow:
Field crew's Before/After image upload with lat/long capture failed on mobile browsers due to permission issues. We added manual lat/long editable fallback and
"Use My Location" with error handling.

Each bug taught us how real railway coordination works - it's not just code, it's about not stopping trains!
