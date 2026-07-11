# 配套代码 — FlagGems Triton 算子开发

本目录为课程报告《基于 Triton 语言的 FlagGems 高性能 GPU 算子开发与优化》的配套代码，
来源于 Kaggle "Track 1: LLM Operator Development and Optimization" 竞赛提交
（FlagGems 仓库 PR #3460，基于 FlagGems v5.0.2）。

## 目录结构

```
code/
├── ops/                          # 算子 Triton 实现 (src/flag_gems/ops/)
│   ├── log10.py                  # [新增] Pointwise 模板范例
│   ├── logaddexp.py              # 类型提升与广播
│   ├── cosh.py / asinh.py        # 双曲函数
│   ├── gcd.py                    # 整数算子 (libdevice)
│   ├── tril.py / roll.py         # 布局/索引算子
│   ├── leaky_relu.py             # 激活函数
│   ├── upsample_nearest2d.py     # [新增 backward] 含 atomic_add 散射梯度
│   ├── conv_transpose2d.py       # [新增 backward] 转置卷积，aten fallback
│   ├── max_pool3d_with_indices.py# [优化] autotune + num_stages 软流水
│   ├── svd.py                    # 困难：Jacobi 旋转 SVD
│   ├── ctc_loss.py               # 困难：CTC 动态规划
│   └── grid_sample.py            # 困难：双线性/最近邻插值
├── fused/
│   └── chunk_gated_delta_rule.py # 困难：线性注意力融合算子 (FLA)
└── tests/                        # pytest 测试套件 (tests/)
    ├── test_logaddexp.py         # [增强] special/empty/noncontiguous/broadcast
    ├── test_leaky_relu.py        # [增强] slopes + special_values
    ├── test_asinh.py             # [增强] noncontiguous
    ├── test_avg_pool3d.py        # [增强] noncontiguous + divisor_override
    ├── test_conv_transpose2d.py  # [增强] noncontiguous + 参数覆盖
    ├── test_max_pool3d.py        # [修复] 移除 skip + 反向精度
    ├── test_pixel_shuffle.py     # [增强] noncontiguous + 多因子
    └── test_upsample_nearest2d.py# [增强] 反向 + 非连续
```

## 环境依赖

| 组件 | 版本 |
|------|------|
| Python | >= 3.10 (cp312) |
| PyTorch | 2.10.0+cu128 |
| Triton | 3.6.0 |
| CUDA Toolkit | 12.8 |
| FlagGems | 5.0.2 |

## 关键改动一览（对应 PR #3460 的 13 个文件）

1. **新增反向传播实现**：`upsample_nearest2d_backward`、`conv_transpose2d_backward`
2. **Bug 修复**：移除 `max_pool3d` 测试错误 skip，修正反向精度容差，注册 `chunk_gated_delta_rule`
3. **测试增强**：8 个测试文件补充非连续张量、特殊值、空张量、广播、参数边界覆盖
4. **性能优化**：`max_pool3d` 反向 autotune 扩展 + num_stages 软流水，关键配置 0.35x → 1.0x+

## 回归测试结果

```
Phase 1 (简单 8):   969  passed, 0 failed
Phase 2 (中等 8):  1634  passed, 0 failed, 120 skipped
Phase 3 (困难 4):   268  passed, 0 failed,  14 skipped
————————————————————————————————————————————
总计 (20 算子):    2871  passed, 0 failed, 134 skipped   (289.36s)
```

## 运行方式

```bash
# 完整 FlagGems 仓库下，启用 Triton 后端
python -c "import flag_gems; flag_gems.enable_gems()"
pytest tests/test_logaddexp.py tests/test_upsample_nearest2d.py -v
```
