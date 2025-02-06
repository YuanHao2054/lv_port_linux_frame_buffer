## 编译指令
```bash
#在./build中执行
# 生成makefile
cmake ..

# 编译
make

#清理工程
make custom_clean
```
# 移植GUI_Guider

# 一、复制GUI_Guider生成的工程

将生成的文件中的这两个文件夹

- custom
- generater

复制到lvgl工程的gui_guider文件夹中

# 二、在CMakeLists.txt中添加gui guider的文件

1. 包含头文件

   ```cmake
   # 添加lvgl.h的头文件路径，主要是GUI_Guider生成的代码需要
   include_directories(${CMAKE_SOURCE_DIR}/lvgl)
   include_directories(${CMAKE_SOURCE_DIR}/lvgl/src)
   include_directories(${CMAKE_SOURCE_DIR}/lvgl/src/font)
   
   # 添加gui_guider.h的头文件路径
   # 如果不使用GUI_Guider，可以注释掉这一行
   include_directories(${CMAKE_SOURCE_DIR}/gui_guider)
   include_directories(${CMAKE_SOURCE_DIR}/gui_guider/custom)
   include_directories(${CMAKE_SOURCE_DIR}/gui_guider/generated)
   ```

2. 添加源文件

   ```cmake
   # 设置源代码目录
   set(LVGL_DIR ${CMAKE_SOURCE_DIR})
   include_directories(${LVGL_DIR})
   
   # 生成源文件列表
   set(MAIN_SRC "main.c")
   set(LVGL_SRCS)
   set(LV_DRIVERS_SRCS)
   set(GUI_GUIDER_SRCS)
   
   # 递归查找子目录下的所有 C 文件
   file(GLOB_RECURSE LVGL_SRCS "${LVGL_DIR}/lvgl/*.c")
   file(GLOB_RECURSE LV_DRIVERS_SRCS "${LVGL_DIR}/lv_drivers/*.c")
   file(GLOB_RECURSE GUI_GUIDER_SRCS "${LVGL_DIR}/gui_guider/*.c") # 添加gui guider的文件
   
   
   # 注释掉编译鼠标输入的源码
   # list(REMOVE_ITEM LV_DRIVERS_SRCS "${LVGL_DIR}/mouse_cursor_icon.c")
   
   # 合并所有源文件
   #set(SRCS ${MAIN_SRC} ${LVGL_SRCS} ${LV_DRIVERS_SRCS})
   set(SRCS ${MAIN_SRC} ${LVGL_SRCS} ${LV_DRIVERS_SRCS} ${GUI_GUIDER_SRCS}) # 合并到SRCS中
   ```

完成上述操作能将gui_guider生成的工程链接到lvgl中，但生成的工程文件本身还会有些报错，需要根据具体情况逐个解决

# 三、复制依赖文件

工程里用的lvgl文件是直接从官方仓库下载的，但是GUI Guider生成的工程会额外依赖一些文件，存放在它生成的工程中的另一个lvgl文件中，编译时如果在链接阶段因缺乏对应文件而报错，需要把GUI Guider生成的工程里的对应文件替换过来