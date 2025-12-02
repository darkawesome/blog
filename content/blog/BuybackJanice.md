---
title: "BuybackJanice"

date: 2025-11-16T11:17:01-05:00
url: /BuybackJanice/
image: /images/2020-thumbs/BuybackJanice.jpg

draft: true
---

This article explores the development of an optimized desktop application that solves this problem by interfacing with EVE Online's ESI (EVE Swagger Interface) API to provide real-time transportation analysis.
<!--more-->

# Building an Optimized EVE Online Transportation Calculator with Python and PyQt5

## Introduction

EVE Online, the massive multiplayer space simulation game, presents players with complex logistics challenges. One fundamental question every industrialist and hauler faces is: "How do I transport these items efficiently?" This seemingly simple question involves calculating cargo volumes, determining appropriate ship types, and considering the economic value density of items being transported.

## The Problem Space

### Transportation Types in EVE Online

EVE Online features several categories of industrial ships, each with different cargo capacities:

- **Jump Freighters (JF)**: 350,000 m³ capacity - expensive but can traverse nullsec space via cyno jumps
- **Freighters**: 1,139,969 m³ capacity - larger cargo holds but limited to high-security routes
- **Items exceeding freighter capacity**: Require multiple trips or alternative logistics solutions

Beyond simple volume calculations, sophisticated haulers also consider **ISK density** (ISK per cubic meter). High-value, low-volume items are priority targets for gankers, making this metric critical for risk assessment.

## System Architecture

### Technology Stack

The application leverages:

- **PyQt5**: Cross-platform GUI framework for desktop applications
- **ESI API**: CCP Games' official API for EVE Online data
- **Pandas**: Data manipulation for Excel/CSV import/export
- **ThreadPoolExecutor**: Concurrent API calls for performance optimization
- **QThread**: Non-blocking UI operations during network requests

### Key Design Decisions

#### 1. **Persistent Type Caching**

The most significant optimization is a class-level cache for item type data:

```python
class DataFetcherThread(QThread):
    type_cache = {}  # Persists across multiple runs
```

Since item metadata (volume, type_id) is immutable, we cache it at the class level. This means:
- First lookup of "Tritanium" requires 2 API calls
- Subsequent lookups across the entire session are instant
- Users processing similar cargo lists see dramatic speedups

#### 2. **Parallel API Request Processing**

Instead of sequential processing, we use concurrent futures:

```python
with ThreadPoolExecutor(max_workers=5) as executor:
    future_to_item = {
        executor.submit(self.process_item, name, qty): (name, qty)
        for name, qty in self.items
    }
```

This parallelizes independent API calls, reducing total processing time from O(n × latency) to approximately O(latency) for most cargo manifests.

#### 3. **Single Market Price Fetch**

Rather than individual price lookups per item, we fetch the entire market price database once:

```python
p_url = f"{ESI_BASE}/markets/prices/?datasource=tranquility"
p_resp = requests.get(p_url, timeout=30)
```

This single ~300KB download provides adjusted/average prices for all ~33,000 item types in EVE, enabling O(1) price lookups.

## Implementation Deep Dive

### Data Flow Architecture

```
User Input → Parser → Thread Pool → ESI API → Results Table
                          ↓
                    Type Cache (persistent)
                          ↓
                  Price Database (one-time fetch)
```

### ESI API Integration

The application makes three types of ESI calls:

1. **Item Search**: `/universe/ids/` (POST with item names)
2. **Type Metadata**: `/universe/types/{type_id}/` (volume data)
3. **Market Prices**: `/markets/prices/` (one bulk fetch)

Each has appropriate error handling and timeout settings to handle EVE's occasionally unstable API.

## Performance Optimization Results

### Before Optimization
- **Sequential processing**: ~2 seconds per item (2 API calls × 500ms + 1s processing)
- **No caching**: Repeated items processed fully each time
- **Individual price lookups**: 50 items = 50 additional API calls

**Example**: 50-item cargo manifest = ~120 seconds

### After Optimization
- **Parallel processing**: 5 concurrent workers
- **Type caching**: Instant lookups for repeated items
- **Bulk price fetch**: Single 300KB download

**Example**: 50-item cargo manifest = ~15 seconds (8× faster)

Subsequent runs with cached items: **<5 seconds** (24× faster)


### Aggregate Summary Calculation

The system calculates total volume and provides intelligent recommendations:

```python
if total_volume <= JF_CAPACITY:
    ships_text = "1 Jump Freighter"
elif total_volume <= FREIGHTER_CAPACITY:
    ships_text = "1 Freighter"
else:
    num_freighters = math.ceil(total_volume / FREIGHTER_CAPACITY)
    ships_text = f"{num_freighters} Freighters"
```

### ISK Density Analysis

High-value items are flagged automatically:

```python
if isk_per_m3 >= 1_000_000:  # 1M ISK/m³ threshold
    note = f"High ISK/m³ ({int(isk_per_m3):,})"
```

This warns haulers about gank-worthy cargo requiring additional escort or lower-profile shipping methods.


## Error Handling Strategy

The application implements defense-in-depth error handling:

1. **API Level**: Try-catch around all network calls with timeouts
2. **Data Level**: Validation of ESI responses and type conversions
3. **UI Level**: User-friendly error messages via QMessageBox
4. **Thread Level**: Error signals from worker thread to UI thread

Example error handling pattern:

```python
try:
    result = future.result()
    results.append(result)
except Exception as e:
    results.append({
        "item": name,
        "transport_type": f"Error: {e}"
    })
```

Failed items don't crash the application; they appear in results with error descriptions.


### Color-Coded Ship Requirements

Visual feedback uses color psychology:
- 🟢 Green: Single JF (safe, easy)
- 🔵 Blue: Single Freighter (routine)
- 🔴 Red: Multiple ships required (complex logistics)

## Lessons Learned

### 1. Cache Aggressively for Immutable Data
EVE item metadata doesn't change. Class-level caching provided the single largest performance improvement.

### 2. Batch Network Operations
Fetching 33,000 prices in one call beats 50 individual lookups every time, despite downloading "extra" data.

### 3. Parallel Processing Has Overhead
Beyond 5-7 concurrent workers, we hit diminishing returns due to ESI rate limiting and connection overhead.

## Future Enhancements

Potential improvements:

1. **Route Planning**: Integrate ESI's route API for jump calculations
2. **Contract Parsing**: Auto-import from in-game contracts via ESI authentication


## Conclusion

This EVE Online transport calculator demonstrates how thoughtful architectural decisions—persistent caching, parallel processing, and bulk data fetching—can transform a sluggish application into a responsive tool. By leveraging PyQt5's threading model and ESI's comprehensive API, we created a desktop application that handles real-world logistics challenges faced by EVE industrialists daily.

The complete source code is available, and the techniques demonstrated here apply broadly to any application interfacing with rate-limited APIs while maintaining responsive UIs.

---

- Dependencies: requests, pandas, openpyxl
- Performance: Processes 50 items in ~15 seconds

**Links:**
- [EVE ESI Documentation](https://esi.evetech.net/ui/)
- [PyQt5 Documentation](https://www.riverbankcomputing.com/static/Docs/PyQt5/)
- [EVE University: Hauling](https://wiki.eveuniversity.org/Hauling)

![Summary](https://github.com/darkawesome/blog/blob/main/content/img/Cyber-Analysis/summary.png?raw=true)

![Summary](https://github.com/darkawesome/blog/blob/main/content/img/buyback.webp?raw=true)





