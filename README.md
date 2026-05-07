# 응급상황 골든타임 사각지대 분석 — 동작구·관악구

Personal replication of the data science.
Analyze emergency medical accessibility blind spots in Dongjak-gu and Gwanak-gu, Seoul.

## 📁 Cấu trúc

```
emergency-blindspot/
├── notebooks/
│   ├── emergency_blindspot_analysis.ipynb           # bản gốc (chưa run)
│   └── emergency_blindspot_analysis_executed.ipynb  # bản đã run với output
├── output/
│   ├── blindspot_map.html    # interactive folium map (mở bằng browser)
│   └── eda_charts.png        # 2 charts EDA
├── data/
│   ├── seoul_neighborhoods_geo_simple.json  # 행정구역 polygon (Seoul 2015)
│   └── seoul-maps-LICENSE.md                # attribution
└── README.md
```

## 🔧 Yêu cầu

- Python 3.10+
- Linux Mint (đã test trên môi trường tương đương). Cần Korean font cho matplotlib:
  ```bash
  sudo apt install fonts-nanum
  fc-cache -fv
  rm -rf ~/.cache/matplotlib  # clear matplotlib font cache
  ```

## 📦 Install dependencies

```bash
pip install pandas numpy geopandas folium matplotlib shapely jupyter
```

Nếu không có `fonts-nanum`, chữ Hàn trong PNG charts sẽ hiển thị thành `□□□`. Folium map (HTML) **không bị ảnh hưởng** vì render bằng browser.

## ▶️ Cách chạy

```bash
cd emergency-blindspot
jupyter notebook notebooks/emergency_blindspot_analysis.ipynb
```

Run all cells. Outputs sẽ được tạo trong `output/`:
- `blindspot_map.html` — mở trong browser để xem interactive map
- `eda_charts.png` — bar + scatter chart

## 📊 Kết quả chính (từ run lần trước)

| Dong | Gu | Dist to nearest ER (km) | Status |
|------|-----|------------------------|---------|
| 남현동 | 관악구 | 4.79 | CRITICAL nhất |
| 사당동 | 동작구 | 2.46 | BLIND_SPOT |
| 신림동 | 관악구 | 2.40 | CRITICAL (do 인구 大) |
| 봉천동 | 관악구 | 2.21 | CRITICAL (do 인구 大) |
| 흑석동 | 동작구 | 0.18 | OK (중앙대 nearby) |

**Hypothesis check:**
1. ✅ Dong별 거리 차이 유의미 (range 0.18 → 4.79 km)
2. ⚠️ Density vs distance correlation — check notebook output
3. ✅ Sác지대 식별 가능: dong > 3km có ~273k 영향 인구

## ⚠️ Limitations

- Phân tích ở mức **법정동** (12 dong), không phải 행정동 (36 dong) như proposal gốc → public GeoJSON 2015 chỉ có tới level này
- **Haversine** (great-circle) thay vì network distance → real driving distance sẽ dài hơn
- **3 ER trong 2 자치구** only → không tính ER ở Yongsan, Yeongdeungpo, Seocho có thể serve
- Population data là **estimate based on 2024 stats**, không phải raw download từ KOSIS
- Chưa có thời gian-of-day variation (rush hour traffic)

## 🚀 V2 Ideas (nếu mày muốn upgrade)

1. **Network distance** với `osmnx` + `networkx` shortest path
2. **Grid 250m** thay vì dong-level (areal weighting)
3. **Two-Step Floating Catchment Area (2SFCA)** score
4. **Inter-gu ER inclusion** (5km buffer ngoài 동작/관악)
5. **Real KOSIS population data** download trực tiếp
6. **p-median** location-allocation cho ER mới

## 📚 Data sources

- ER 위치: 응급의료포털 E-Gen + 서울시 응급실 위치 정보 (manual verification via Naver Map)
- 행정구역: https://github.com/southkorea/seoul-maps (CC BY)
- 인구: 행정안전부 주민등록 인구통계 (estimate 2024)

## 📝 License

Personal study project. Code for educational use.
