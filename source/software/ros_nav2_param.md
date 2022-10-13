Navigation2 파라메터 정보
====

- teb local planner 파라미터 
  - 파라미터 설정 참고 링크
    - <https://mowito-navstack.readthedocs.io/en/latest/step_5c.html>
    - <https://docs.ros.org/en/melodic/api/teb_local_planner/html/classteb__local__planner_1_1TebConfig.html>
    - <https://github.com/IntelligentRoboticsLabs/marathon_ros2/blob/master/marathon_ros2_bringup/params/nav2_marathon_teb_tiago_params.yaml>
    - <https://github.com/wookbin/TETRA-DS5/blob/master/tetraDS_2dnav/config/teb_local_planner_params.yaml>

  - nav2 스택의 controller_server 에서 teb local planner가 사용되며 관련 파라미터는 아래와 표와같다.  
  
  |      종류       | 설명                                                                                                                                      | 관련 파라미터                                                                                                                                                                  |
  | :-------------: | :---------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
  |   컨트롤 주기   | 10.0[Hz]                                                                                                                                  | controller_frequency                                                                                                                                                           |
  | 도착 목표 오차  | xy: 0.25[m] <br> yaw: 0.25[rad]                                                                                                           | xy_goal_tolerance: 0.25 <br>yaw_goal_tolerance: 0.25                                                                                                                           |
  | 로봇 kinematics | 최대 속도: 0.5[m/s] <br> 최대 각속도: 0.4[rad/s] <br> 가속도: 2.0[m/s] <br> 가각속도: 2.0[rad/s]<br> 로못 모델: 타입-원형, 반지름-0.32[m] | max_vel_x: 0.5<br>max_vel_x_backwards: 0.0<br>max_vel_theta: 0.4<br>acc_lim_x: 2.0<br>acc_lim_theta: 2.0<br> footprint_model.type: "circular"<br> footprint_model.radius: 0.32 |
  |   장애물 관련   | 최소 장애물 이격 거리:0.25[m]<br>장애물 인퓰레이션 거리:0.30[m]                                                                           | min_obstacle_dist: 0.25 <br> inflation_dist: 0.30                                                                                                                              |

- CostMap(Global, Local)
  - 센서 및 맵 정보를 활용한 코스트 맵의 파라미터이다.
  
  - Local & Global Costmap

    |   종류    | 설명                               | 관련 파라미터                                     |
    | :-------: | :--------------------------------- | :------------------------------------------------ |
    | 동작 주기 | 업데이트: 10.0Hz <br> 발행: 10.0Hz | update_frequency: 10.0<br>publish_frequency: 10.0 |
    | 맵 크기 | 15mx15m | width: 15 <br>  height: 15|
    | 해상도 | 0.03[m] |  resolution: 0.03 | 

    - obstacle_layer
      - 라이다를 사용한 장애물 감지 레이어

      |       종류       | 설명                                      | 관련 파라미터                                         |
      | :--------------: | :---------------------------------------- | :---------------------------------------------------- |
      | 관찰 데이터 소스 | 토픽: /scan (라이다 스캔 정보)            | topic: /scan                                          |
      |   장애물 인식    | 최대 거리: 15.0[m] <br> 최소 거리: 0.0[m] | obstacle_max_range: 15.0 <br> obstacle_min_range: 0.0 |
      |     raytrace     | 최대 거리: 15.5[m] <br> 최소 거리: 0.0[m] | raytrace_max_range: 15.0 <br> raytrace_min_range: 0.0 |

    - stvl_layer
      - realsense RGBD 카메라 포인트 클라우드 사용 장애물 감지 레이어
      - <https://github.com/SteveMacenski/spatio_temporal_voxel_layer>

      |                                            종류                                            | 설명                                                                                      | 관련 파라미터 |
      | :---: | :------- | :------------ |
      | 복셀 설정   | decay: 1. [sec] <br> model: 0 (0=linear, 1=exponential, -1=persistent) <br> size: 0.01[m] <br> 최소 인정 복셀 군집: 0[갯수], (z 축기준 최소 0개 이상의 복셀이 있을때 장애물로 인식) | voxel_decay: 1. <br> decay_model: 0  <br>voxel_size: 0.01  <br>mark_threshold: 0|
      | 관찰 데이터 소스 | 토픽: /camera_front_down/depth/color/points (Realsense)            | topic: /camera_front_down/depth/color/points |
      | 장애물 인식 | 인식 거리: 3.0[m] <br> 최소 인식 높이: 0.05[m] <br> 최대 인식 높이: 0.8[m] | obstacle_range: 3.0 <br> min_obstacle_height: 0.05 <br> max_obstacle_height: 0.8 |
      | 기타 | 복셀 인정 최소 point 군집: 3[개수] (최소 pointcloud 3개 이상 군집 되어있어야 1개 의 복셀로 인정) <br> FOV(수평/수직): 0.8745[rad] / 1.048[rad]<br> decay 가속값: 10.0 (0 보다 커야한다.)| vertical_fov_angle: 0.8745 <br> horizontal_fov_angle: 1.048 <br> decay_acceleration: 10.|

    - inflation_layer
      - <http://wiki.ros.org/costmap_2d/hydro/inflation>
      - inflation_radius: 1.75[m] 
        - 장애물 주변으로 생성될 코스트의 거리(반지름) 
      - cost_scaling_factor: 3.58
        - 높아질수록 장애물 주변 inflation 코스트가 감소
        - 더 장애물에 가깝게 진행하고 싶다면 값이 커야한다.

    - static_layer
      - Map 정보를 사용한 장애물 감지 레이어 
      - 별도 설정 없이 레이어만 선안하여 default 값 사용 


example 
------
```bash
bt_navigator:
  ros__parameters:
    use_sim_time: true
    global_frame: map
    robot_base_frame: base_footprint
    transform_tolerance: 0.5
    default_nav_through_poses_bt_xml: "/home/nvidia/robot/exa/ros_ws/src/ExaRobot_ROS2/exa_robot/config/navigate_through_poses_w_replanning_and_recovery.xml"
    default_nav_to_pose_bt_xml: "/home/nvidia/robot/exa/ros_ws/src/ExaRobot_ROS2/exa_robot/config/navigate_to_pose_w_replanning_and_recovery.xml"
    #default_nav_to_pose_bt_xml: "/home/nvidia/robot/exa/ros_ws/src/ExaRobot_ROS2/exa_robot/config/navigate_follow_point.xml"

    goal_blackboard_id: goal
    goals_blackboard_id: goals
    path_blackboard_id: path
    plugin_lib_names:
    #action
    - nav2_back_up_action_bt_node
    - nav2_clear_costmap_service_bt_node
    - nav2_compute_path_through_poses_action_bt_node
    - nav2_compute_path_to_pose_action_bt_node
    - nav2_controller_selector_bt_node
    - nav2_follow_path_action_bt_node
    - nav2_goal_checker_selector_bt_node
    - nav2_navigate_through_poses_action_bt_node
    - nav2_navigate_to_pose_action_bt_node
    - nav2_planner_selector_bt_node
    - nav2_reinitialize_global_localization_service_bt_node
    - nav2_remove_passed_goals_action_bt_node
    - nav2_spin_action_bt_node
    - nav2_truncate_path_action_bt_node
    - nav2_wait_action_bt_node
    #condition
    - nav2_distance_traveled_condition_bt_node
    - nav2_goal_reached_condition_bt_node
    - nav2_goal_updated_condition_bt_node
    - nav2_initial_pose_received_condition_bt_node
    - nav2_is_battery_low_condition_bt_node
    - nav2_is_stuck_condition_bt_node
    - nav2_time_expired_condition_bt_node
    - nav2_transform_available_condition_bt_node
    #control
    - nav2_pipeline_sequence_bt_node
    - nav2_recovery_node_bt_node
    - nav2_round_robin_node_bt_node
    #decorator
    - nav2_distance_controller_bt_node
    - nav2_goal_updater_node_bt_node
    - nav2_rate_controller_bt_node
    - nav2_single_trigger_bt_node
    - nav2_speed_controller_bt_node

bt_navigator_rclcpp_node:
  ros__parameters:
    use_sim_time: True


controller_server:
  ros__parameters:
    odom_topic: /odom
    use_sim_time: True
    controller_frequency: 10.0
    controller_plugin_types: ["teb_local_planner::TebLocalPlannerROS"]
    controller_plugins: ["FollowPath"]
    FollowPath:
      plugin: "teb_local_planner::TebLocalPlannerROS"

      teb_autosize: 1.0
      dt_ref: 0.3
      dt_hysteresis: 0.03 #0.1
      max_samples: 50 #500
      global_plan_overwrite_orientation: True #False
      allow_init_with_backwards_motion: False
      max_global_plan_lookahead_dist: 5.0 #3.0
      global_plan_viapoint_sep: 1.0 #0.3
      global_plan_prune_distance: 1.0
      exact_arc_length: False
      feasibility_check_no_poses: 3 #2
      publish_feedback: False
          
      # Robot
      max_vel_x: 0.5
      max_vel_x_backwards: 0.0
      max_vel_theta: 0.4
      acc_lim_x: 2.0
      acc_lim_theta: 2.0

      footprint_model: # types: "point", "circular", "two_circles", "line", "polygon"
        type: "circular"
        # types: "polygon"
        radius: 0.32 # for type "circular"
        # vertices: [[0.202,0.245], [-0.439,0.245], [-0.439,-0.245], [0.202,-0.245]]
 
      # GoalTolerance
      xy_goal_tolerance: 0.25
      yaw_goal_tolerance: 0.25  
      free_goal_vel: False
          
      # Obstacles
          
      min_obstacle_dist: 0.32 #0.44 #0.27
      inflation_dist: 0.30 #0.5 #0.6
      include_costmap_obstacles: True
      costmap_obstacles_behind_robot_dist: 1.5 #1.0
      obstacle_poses_affected: 30 #15

      dynamic_obstacle_inflation_dist: 0.2 #0.4 #0.6
      include_dynamic_obstacles: False #True 

      costmap_converter_plugin: "costmap_converter::CostmapToPolygonsDBSMCCH"
      costmap_converter_spin_thread: True
      costmap_converter_rate: 5

      # Optimization
          
      no_inner_iterations: 5
      no_outer_iterations: 4
      optimization_activate: True
      optimization_verbose: False
      penalty_epsilon: 0.04 #0.1
      obstacle_cost_exponent: 4.0
      weight_max_vel_x: 1.0 #0.5
      weight_max_vel_theta: 1.0 #0.5
      weight_acc_lim_x:  0.0 #0.5
      weight_acc_lim_theta: 0.0 #10.5
      weight_kinematics_nh: 1000.0
      weight_kinematics_forward_drive: 1000.0 #3.0
      weight_kinematics_turning_radius: 1.0
      weight_optimaltime: 1.0 # must be > 0
      weight_shortest_path: 0.0
      weight_obstacle: 10.0 #100.0
      weight_inflation: 0.2
      weight_dynamic_obstacle: 10.0 # not in use yet
      weight_dynamic_obstacle_inflation: 0.2
      weight_viapoint: 1.0 #50.0
      weight_adapt_factor: 2.0

      # Homotopy Class Planner

      enable_homotopy_class_planning: false #True
      enable_multithreading: True
      max_number_classes: 4
      selection_cost_hysteresis: 1.0 #5.0
      selection_prefer_initial_plan: 1.0
      selection_obst_cost_scale: 1.0
      selection_alternative_time_cost: False #True

      roadmap_graph_no_samples: 15
      roadmap_graph_area_width: 5.0
      roadmap_graph_area_length_scale: 1.0
      h_signature_prescaler: 0.5
      h_signature_threshold: 0.1
      obstacle_heading_threshold: 0.45
      switching_blocking_period: 0.0
      viapoints_all_candidates: True
      delete_detours_backwards: True
      max_ratio_detours_duration_best_duration: 3.0
      visualize_hc_graph: False
      visualize_with_time_as_z_axis_scale: 0.0

      # Recovery
      
      shrink_horizon_backup: True
      shrink_horizon_min_duration: 10.0
      oscillation_recovery: True
      oscillation_v_eps: 0.1
      oscillation_omega_eps: 0.1
      oscillation_recovery_min_duration: 10.0
      oscillation_filter_duration: 10.0
     
controller_server_rclcpp_node:
  ros__parameters:
    use_sim_time: True



local_costmap:
  local_costmap:
    ros__parameters:
      update_frequency: 10.0
      publish_frequency: 10.0
      global_frame: odom
      robot_base_frame: base_footprint
      use_sim_time: True
      rolling_window: true
      width: 15
      height: 15
      resolution: 0.03
      robot_radius: 0.22
      unknown_cost_value : 200
      plugins: [
                "obstacle_layer",
                "stvl_layer",
                "inflation_layer"
                ]
      # plugins: ["voxel_layer","stvl_layer","inflation_layer"]
      # plugins: ["voxel_layer", "inflation_layer"]



      obstacle_layer:
        plugin: "nav2_costmap_2d::ObstacleLayer"
        enabled: true
        observation_sources: scan
        scan:
          topic: /scan
          max_obstacle_height: 2.0
          clearing: True
          marking: True
          data_type: "LaserScan"
          raytrace_max_range: 15.5
          raytrace_min_range: 0.0
          obstacle_max_range: 15.0
          obstacle_min_range: 0.0
     
        
      stvl_layer:
        plugin: "spatio_temporal_voxel_layer/SpatioTemporalVoxelLayer"
        enabled: true
        voxel_decay: 1.
        decay_model: 0
        voxel_size: 0.01
        track_unknown_space: true
        unknown_threshold: 15
        mark_threshold: 0
        update_footprint_enabled: true
        combination_method: 1
        origin_z: 0.0
        publish_voxel_map: true
        transform_tolerance: 0.2
        mapping_mode: false
        map_save_duration: 60.0
        observation_sources: pointcloud clip
        pointcloud:
          data_type: PointCloud2
          topic: /camera_front_down/depth/color/points
          marking: true
          clearing: true
          obstacle_range: 3.0
          min_obstacle_height: 0.05
          max_obstacle_height: 0.8
          expected_update_rate: 0.0
          observation_persistence: 0.0
          inf_is_valid: false
          voxel_filter: true
          voxel_min_points: 3 
          clear_after_reading: true
          # max_z: 2.0
          # min_z: 0.08
          vertical_fov_angle: 0.8745
          horizontal_fov_angle: 1.048
          decay_acceleration: 30.0
          model_type: 0
      clip: 
          data_type: PointCloud2
          topic: /camera_front_down/depth/color/points
          marking: true
          clearing: true
          obstacle_range: 2.0
          min_obstacle_height: -0.3
          max_obstacle_height: -0.2
          expected_update_rate: 0.0
          observation_persistence: 0.0
          inf_is_valid: false
          voxel_filter: true
          clear_after_reading: true
          # max_z: 2.0
          min_z: -0.5
          vertical_fov_angle: 0.8745
          horizontal_fov_angle: 1.048
          decay_acceleration: 1.0
          model_type: 0
      inflation_layer:
        plugin: "nav2_costmap_2d::InflationLayer"
        # cost_scaling_factor: 3.0
        # inflation_radius: 0.30
        cost_scaling_factor: 3.58 #2.58
        inflation_radius: 1.75 #1.75

      always_send_full_costmap: True
  local_costmap_client:
    ros__parameters:
      use_sim_time: True
  local_costmap_rclcpp_node:
    ros__parameters:
      use_sim_time: True

global_costmap:
  global_costmap:
    ros__parameters:
      width: 10
      height: 10
      update_frequency: 10.0
      publish_frequency: 10.0
      global_frame: map
      robot_base_frame: base_footprint
      use_sim_time: True
      robot_radius: 0.22
      resolution: 0.05
      track_unknown_space: true
      unknown_cost_value : 200
      plugins: ["static_layer",
                "obstacle_layer",
                "stvl_layer", 
                "inflation_layer"]

      obstacle_layer:
        plugin: "nav2_costmap_2d::ObstacleLayer"
        enabled: True
        observation_sources: scan
        scan:
          topic: /scan
          max_obstacle_height: 2.0
          clearing: True
          marking: True
          data_type: "LaserScan"
          raytrace_max_range: 15.5
          raytrace_min_range: 0.0
          obstacle_max_range: 15.0
          obstacle_min_range: 0.0

      stvl_layer:
        plugin: "spatio_temporal_voxel_layer/SpatioTemporalVoxelLayer"
        enabled: true
        voxel_decay: 1.
        decay_model: 0
        voxel_size: 0.01
        track_unknown_space: true
        unknown_threshold: 15
        mark_threshold: 0
        update_footprint_enabled: true
        combination_method: 1
        origin_z: 0.0  
        publish_voxel_map: true
        transform_tolerance: 0.2
        mapping_mode: false
        map_save_duration: 60.0
        observation_sources: pointcloud clip
        pointcloud:
          data_type: PointCloud2
          topic: /camera_front_down/depth/color/points
          marking: true
          clearing: true
          obstacle_range: 3.0
          min_obstacle_height: 0.05
          max_obstacle_height: 0.8
          expected_update_rate: 0.0
          observation_persistence: 0.0
          inf_is_valid: false
          voxel_filter: true
          voxel_min_points: 3  
          clear_after_reading: true
          # max_z: 2.0
          # min_z: 0.05
          vertical_fov_angle: 0.8745
          horizontal_fov_angle: 1.048
          decay_acceleration: 30.0
          model_type: 0
      clip: 
          data_type: PointCloud2
          topic: /camera_front_down/depth/color/points
          marking: true
          clearing: true
          obstacle_range: 2.0
          min_obstacle_height: -0.3
          max_obstacle_height: -0.2
          expected_update_rate: 0.0
          observation_persistence: 0.0
          inf_is_valid: false
          voxel_filter: true
          clear_after_reading: true
          # max_z: 2.0
          min_z: -0.5
          vertical_fov_angle: 0.8745
          horizontal_fov_angle: 1.048
          decay_acceleration: 10.0
          model_type: 0
      static_layer:
        plugin: "nav2_costmap_2d::StaticLayer"
        map_subscribe_transient_local: True
       

      inflation_layer:
        plugin: "nav2_costmap_2d::InflationLayer"
        # cost_scaling_factor: 3.0
        # inflation_radius: 0.30
        cost_scaling_factor: 3.58 #2.58
        inflation_radius: 1.75 #1.75
        inflate_unknown : True

      always_send_full_costmap: True
  global_costmap_client:
    ros__parameters:
      use_sim_time: True
  global_costmap_rclcpp_node:
    ros__parameters:
      use_sim_time: True

map_server:
  ros__parameters:
    use_sim_time: True
    yaml_filename: "map.yaml"

map_saver:
  ros__parameters:
    use_sim_time: True
    save_map_timeout: 5.0
    free_thresh_default: 0.25
    occupied_thresh_default: 0.65
    map_subscribe_transient_local: True


planner_server:
  ros__parameters:
    expected_planner_frequency: 10.0
    use_sim_time: True
    planner_plugins: ["GridBased"]
    GridBased:
      plugin: "nav2_navfn_planner/NavfnPlanner"
      tolerance: 0.5
      use_astar: false
      allow_unknown: false

planner_server_rclcpp_node:
  ros__parameters:
    use_sim_time: True

recoveries_server:
  ros__parameters:
    costmap_topic: local_costmap/costmap_raw
    footprint_topic: local_costmap/published_footprint
    cycle_frequency: 10.0
    recovery_plugins: ["spin", "backup", "wait"]
    spin:
      plugin: "nav2_recoveries/Spin"
    backup:
      plugin: "nav2_recoveries/BackUp"
    wait:
      plugin: "nav2_recoveries/Wait"
    global_frame: odom
    robot_base_frame: base_footprint
    transform_timeout: 0.1
    use_sim_time: true
    simulate_ahead_time: 2.0
    max_rotational_vel: 1.0
    min_rotational_vel: 0.4
    rotational_acc_lim: 3.2

robot_state_publisher:
  ros__parameters:
    use_sim_time: True

waypoint_follower:
  ros__parameters:
    loop_rate: 20
    stop_on_failure: false
    waypoint_task_executor_plugin: "wait_at_waypoint"   
    wait_at_waypoint:
      plugin: "nav2_waypoint_follower::WaitAtWaypoint"
      enabled: True
      waypoint_pause_duration: 200
```