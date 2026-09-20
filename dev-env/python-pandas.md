# pandas 数据处理片段

## 常用片段

```python
# 读取 Excel 并指定 dtype，防止 ID 列被读成数字
df = pd.read_excel("data.xlsx", dtype={"patient_id": str})

# 按条件筛选并去重
df = df[df["modality"].notna()].drop_duplicates(subset="patient_id")

# 分组统计并导出
df.groupby("modality").size().to_excel("stats.xlsx")
```

## 更多片段

```python
# 缺失值填固定字符串（如 modality 缺失填 'blank' 保留原状）
df["modality"] = df["modality"].fillna("blank")

# 多表按 key 合并
merged = df1.merge(df2, on="patient_id", how="left")
```

## 踩坑记录

- （示例）`SettingWithCopyWarning`：筛选后先 `.copy()` 再赋值。
- 导出 XLSX 而非 TSV/CSV，避免 WPS/Excel 打开时格式错乱。
-
