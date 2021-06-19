# V-SLAM Introduction
## Def of SLAM
- Sensor data process
- Visual Odometry
  - 估計相機的相對運動(當下時刻到下個時刻)把運動矩陣跟變化矩陣算出來
- Backend(Optimization)
  - 對視覺里程器的數據進行優化
- Mapping
- Loop clousre detection
  - 探測相機繞了一圈回來
## vSLAM research status:
- Basically solved in theory: filter & optimization
- Several qualified open source frameworks

## Visual Odometry
- Feature-base Methods
- Steps:
  1. Extract feature **key-points** and **descriptors**.
      - Common features: FAST, SIFT, SURF, ORB
  2. Find the **corresponding** matches.
      - Brute-force or kNN match.
      - 相機動太快
      - 場景一片黑沒有特徵點
  3. Estimate the ego-motion.
       - PnP or bundle adjustment.
       - Assume a point X is observed in tow frames whose pixel positions are $x_1 - x_2$
       - $X = [x, y, z]^T$
       - $x_1 = [u_1, v_1]$, $x_2 = [u_2, v_2]^T \epsilon  R^2$
       - $C = \begin{bmatrix}
            f_x & 0 & c_x \\
            0 & f_y & c_y \\
            0 & 0 & 1 \end{bmatrix}$
       - Know: $x_1, \ x_2, \ C$  Goal: $R, \ t, \ X$ 
       - $C(RX+t)$
- So in visual odometry, we
  - Track feature points(lines, planes)
    - 根據觀察frame and frame位移變化
  - Estimate the ego-motion between consecutive frames
  - Reconstruct the local map(sparse key-points or dense models)
- But they may:
  - **Drift** during the motion
    - odometry 你要轉90度，但它實際上轉89度，時間長後會累積誤差造成飄移。
    飄移後無法得到global solutions, 所以需要一個後端去做全局優化
  - **Inconsistent** with other parts of environments
  - **Lost** due to occlusion or fast motion.
  - Global optimization
  - Loop closure
  - Re-localization

## Optimization
- in history, we have two kinds of back-ends
  - Filter
  - Optimization
  > Extended Kalman Filter
  >> motion: $x_p^t = f()$
  >> observation:
- Global optimization: reduce the drifts
  - **Full-SLAM** or **Graph-based SLAM**
  - Considering all the past observations and put them into a large optimization problem.
  - Usually represented as a Graph:
  - $G = (V, \ E)$  
  - **Vertex:** the optimization variables(節點)
    - robot pose update 
    - landmark路標點 update
    - 藍色代表邊edge pose and pose 約束
    - 黑色帶表節點 優化的變量 robot pose 
  - **Edges:** the error terms(or constraints)
    - 節點兩個之間的約束 
    - 纖維值結構 代表 loop closure 越多代表圖的結構會更好 誤差會更小
  - Put them together to build a error function
    - 加入sensor to graph: IMU...
  - Advantages:
    - Use more **information** than filters.
    - Convenient to represent the **loops**.
    - **Sparse** structure in the graph helps computation!
    - **Mix** different vertices and edges!
  - Disadvantage:
    - Hard to maintain the graph size.
      - Graph 圖會不斷增大
    - Global optimization still needs more computation time comparing with filters.
    - 對graph維護
  - Tools: g2o, ceres, etc.
  - A large MSE:

  - Problems in graph optimization:
    - what happens if i add wrong edge into the graph?
      - Robust error term.
    - what if VO gets lost?
      - Meassurement of the connectivity.
    - The graph will grow over time.
      - Long-term SLAM, need pruning.
    - How to fuse two different graph?
      - Multi-robot SLAM.
## Loop Clousre
- The key of closing loops in vSLAM:
- Are the positions of cameras near to each other?
- are the images look the same?

- Odometry-based Approaches
  - Assume the estimated posts are accurate enough.
  - Recursive in logic.
  - 到跟之前差不多的地方，可能會有loop closure
  - 前提你里程計很準，邏輯會有問題
- Appearance-based Approaches
  - Only consider the observed image.
  - State of the art in vSLAM. 
  - 完全不考慮位置為子，只關心圖像是不是像似
- How to measure the similarity of images?
  1. From many image: nose, eyes, hairs, ...
  2. Dictionary
    - 用來描述 比如有一個單描述眼睛 一個描述嘴巴 一個描述
  3. Face = 1 nose + 2 eyes + 1 hair + ...
  4. Features -> Words
    - 主要是把2D image 轉成 1D vector
- We need more complicated models: Bag-of-Words
- Big-of-Words loop closure approaches:
  1. A large image set
  2. Features: SIFT, ORB
  3. image A, image B -> Dictionary -> Words A = 2 car + 1 people Words = 1 car + 1 cat 
  4. Compute Similarity and Raise loop hypothesis
- How to evaluate loop closure methods?
  - Preclision-recall curve.
    - 評估 loop closure好壞 
- Approaches from ML: Auto-encoder, CNNs, etc.
  - 比Dictionary
  - 通過場景比較細緻的分類
## Map
- Maps used in Navigation:
  - Occupancy maps: model the possibility if a point is occupied
    - 2D Occupancy map(ROS)
    - 3D Occupancy map(Octomap)
- Maps used in Reconstruction: (建模)
  - TSDF(Truncated signed distance function)
  - Surfels 
## Conclusion
- in a typical SLAM system, we use
  - **VO** to estimate the ego-motion betwwen frames
  - **Optimization** to handle the global trajectory
  - **Loop clousre** to correct the draft
  - **Map** to describe the environment
- is that all about SLAM?
  - We haven't talked about coding yet.
- Qualified open-source SLAM solutions
  - Rgbd-slam-v2
  - ORB-slam
  - LSD-slam
  - hector-slam
  - SVO
  - RTBslam
  - Dvo-slam
  - kinect Fusion
  - kinfu_large_scale
  - DTAM
- For a student starting to work in SLAM, one should lern:
  - Math: geometry, probabilistic even ML
  - Coding skills:
    - Linux
    - C++
    - Libraries: ROS, OpenCV, PCL, g2o, DBoW2, libpointmatcher, octomap, Fabmap, ceres...
    - Python
- Future issues:
  - Dynamic, Multi-robots, Semantic, Low-weight devices, Mobile platforms