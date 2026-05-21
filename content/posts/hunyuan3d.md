+++
date = '2026-03-19T21:55:15+08:00'
draft = false
title = '混元3D本地部署踩坑'
+++
## 系统：Windows 11，Python 3.10
1. 部分库pypi没有对应版本，需要手动下载whl
 - bpy
 - deepspeed
2. 必须安装Visual Studio 2022，组件选择`生成工具、CMake、Windows SDK`
3. 安装custom_rasterizer时，需要修改路径到custom_rasterizer_for_windows
4. 修改data_ptr<long>到data_ptr<int64_t>
5. 安装DifferentiableRenderer时，需要将sh变为setup.py编译，[详情](https://chat.qwen.ai/c/60a6403b-9753-4d85-a8fc-057b74d455f1)
6. 提前下载模型ckpt放到`%userprofile%\.cache\hy3dgen\tencent\Hunyuan3D-2.1\hunyuan3d-dit-v2-1`
7. 运行服务器
`python3 gradio_app.py --model_path tencent/Hunyuan3D-2.1 --subfolder hunyuan3d-dit-v2-1 --texgen_model_path tencent/Hunyuan3D-2.1 --low_vram_mode`


[官方试用网站](https://huggingface.co/spaces/tencent/Hunyuan3D-2)