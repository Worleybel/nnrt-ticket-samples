# HarmonyOS NNRt 工单复现样本

华为开发者工单（CANN Kit / Kirin 9020 离线模型推理问题）技术支持分析用样本。
工单结案后本仓库将删除；如需长期保留请在工单中告知。

## 文件清单

| 文件 | 说明 |
|---|---|
| `v8_cut35.onnx` | 原模型最小复现件（音频分离模型 209 节点标准前缀子图，含 Conv/InstanceNorm/Erf/ReduceMean） |
| `cut35_fp32.omc` | 上模型经官方 tools_omg（DDK 6.0.1.0，`--target=omc --platform kirin9020`）默认转换产物，与设备上复现 `RunSync` 挂死/SEGV 的样本字节一致 |
| `omg_cut35_fp32.log` | 上行转换完整日志：含 `CheckSupported: InstanceNorm×24 / Convolution×16 / ReduceMean×4 not supported in npucl store` 与 `Partition of type: NPUCL is more than 9, will rollback to CPU` |
| `omg_v8std_weightfp16.log` | 全量 fp32 模型 `--weight_data_type=FP16` 转换日志（产物与默认转换同为 211,591,070 字节） |
| `omg_fp16mix.log` | 源头 fp16 权重模型转换日志（133MB 产物，设备侧被 CheckFromFile 判 INCOMPATIBLE） |
| `size_evidence.txt` | 产物字节数对比说明（omc 内嵌时间戳，字节数为有效对比信号） |

## 环境

- 设备：HUAWEI Mate 70 Pro+（PLA-AL10），固件 7.0.0.111(SP12C00E111R5P3)，Kirin 9020
- 工具链：CANN Kit DDK-tools-6.0.1.0（tools_omg + kirin9020-plugin-next-6.0.1.0）
- 设备侧：`OH_NN*` C API（libneural_network_core.so）

原模型为开源音频源分离模型权重的标准前缀子图（截断于 tencoder.3 内部），仅用于问题复现。
