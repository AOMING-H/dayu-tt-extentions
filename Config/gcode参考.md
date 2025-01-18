以下为TT310参考gcode, 其它尺寸请自行调整

打印起始:
```
M190 S[first_layer_bed_temperature] ; 等待热床升温
M109 S150 ; 先升温到150

; 初始化MMU相关的宏
MMU_START_SETUP INITIAL_TOOL={initial_tool} REFERENCED_TOOLS=!referenced_tools! TOOL_COLORS=!colors! TOOL_TEMPS=!temperatures! TOOL_MATERIALS=!materials! PURGE_VOLUMES=!purge_volumes!

; 调用MMU开始检查
MMU_START_CHECK

EUCLID_PROBE_BEGIN_BATCH  ; klicky 批量模式开启
BED_MESH_CLEAR ; 卸载网床
G28 ; 各轴归位
G90 ; 启用绝对坐标
Z_TILT_ADJUST ; 三点调平
BED_MESH_PROFILE LOAD='default' ; 加载网床
G28 ; 各轴归位
EUCLID_PROBE_END_BATCH  ; klicky 批量模式结束

G1 X5 Y310 F5000.0  ; 移动到擦嘴位置

M109 S[first_layer_temperature] ; 等待挤出机升温
#加载第一个耗材
MMU_START_LOAD_INITIAL_TOOL

G92 E0 ; 重置挤出机
G1 X20 Y270 Z0.3 F5000 ; 移动到第一条线开始
G1 X20 Y200 Z0.2 F1500 E15 ; 画第一条线
G1 X20 Y200 Z0.3 F5000 ; 往上面移动一点准备画第二条线
G1 X20 Y270 Z0.2 F1500 E15 ; 画第二条线
G92 E0 ; 重置挤出机
G1 Z1.0 F500 ; 抬升Z轴，准备移动到模型打印区域
```

打印结束:
```
G91 ; 启用相对位置
G0 Z1.00 X20.0 Y20.0 F5000    ; 移动喷嘴以拆除架线
M107  ; 关闭模型风扇
G1 Z2 F500    ; 抬高Z轴

MMU_END ; 关闭mmu
TURN_OFF_HEATERS ; 关闭加热

G90   ; 启用绝对位置
G1 X20 F5000
G1 Y250 F5000 ; 停靠打印头

BED_MESH_CLEAR  ; 卸载床网
;M84 ; 关闭电机
PRINT_END ; 打印结束
```

取消打印:
```
BED_MESH_CLEAR  ; 卸载床网
MMU_END ; 结束换色
TURN_OFF_HEATERS
CANCEL_PRINT_BASE
G1 X20 F5000
G1 Y250 F5000 ; 移动到停泊位置
M106 S0 ; 关闭模型风扇
#M84 ; 关闭电机
```