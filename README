## _CHRIPS Script_

# CHIRPS Data and Calculate in Watershed WAY Sekampung, Lampung Province
**`Abd. Malik A. Madinu`**
<br /> _Department of Geophysics and Meteorology, Faculty of Mathematics and Natural Sciences, IPB University_
<br /> _SSRS Fellow RO11 Dynamic Climate Research Group, IPB Sustainable Science Research Students Association (IPB SSRS Association), IPB University_
<br /> _SSRS Indonesia Tropic and Ecological Observatory, IPB Sustainable Science Research Students Association (IPB SSRS Association), SSRS Group_

## Research Overview 
Null.....

## Methodology 
Nulll....


## `Script GEE`
Access: https://code.earthengine.google.com/57a2be0a41bc1b6ea5c97b40648d4d09

## Script in Google Earth Engine 
```javascript
// Memuat data curah hujan harian CHIRPS untuk 2015-2024 dalam ROI
var chirps = ee.ImageCollection("UCSB-CHG/CHIRPS/DAILY")
              .filterDate('2015-01-01', '2024-12-31')
              .filterBounds(roi);

print('Jumlah gambar harian CHIRPS:', chirps.size());

// Mendefinisikan daftar tahun dan bulan
var years = ee.List.sequence(2015, 2024);
var months = ee.List.sequence(1, 12);
var monthNames = ee.List(['Jan', 'Feb', 'Mar', 'Apr', 'Mei', 'Jun', 'Jul', 'Agu', 'Sep', 'Okt', 'Nov', 'Des']); // Definisikan sebagai ee.List

// =======================================================================
// 1. Total curah hujan kumulatif selama 2015-2024: jumlah piksel dan jumlah ROI
// =======================================================================
var totalCumulativeRainfall = chirps.sum().clip(roi);

// Menjumlahkan semua nilai piksel dalam ROI menggunakan reduceRegion dengan pengurang sum
var roiTotalCumulativeSum = totalCumulativeRainfall.reduceRegion({
  reducer: ee.Reducer.sum(),
  geometry: roi,
  scale: 1000,
  maxPixels: 1e13,
  bestEffort: true
});

print('1. Total curah hujan kumulatif (jumlah piksel) selama 2015-2024:', totalCumulativeRainfall);
print('2. Total curah hujan kumulatif (jumlah ROI dalam mm):', roiTotalCumulativeSum);

// =======================================================================
// 2. Menghitung curah hujan tahunan dan faktor erosivitas R --->> sum() to sum()
// =======================================================================
var annualRainfallImages = years.map(function(year) {
  year = ee.Number(year);
  // Menjumlahkan curah hujan harian untuk tahun yang diberikan
  var annualSum = chirps
                  .filter(ee.Filter.calendarRange(year, year, 'year'))
                  .sum()
                  .clip(roi)
                  .set('year', year);
  return annualSum;
});

var annualRainfallCollection = ee.ImageCollection.fromImages(annualRainfallImages);

// Menghitung total curah hujan tahunan untuk setiap tahun dan menghitung R
var annualRainfallFeatures = annualRainfallCollection.map(function(image) {
  var total = image.reduceRegion({
    reducer: ee.Reducer.sum(),
    geometry: roi,
    scale: 1000,
    maxPixels: 1e13,
    bestEffort: true
  }).get('precipitation');

  // Menghitung R menggunakan rumus R = 0.5 * P
  var R = ee.Number(total).multiply(0.5);

  return ee.Feature(null, {
    year: image.get('year'),
    totalRainfall: total,
    erosivityFactorR: R
  });
});

// Mengonversi ke FeatureCollection
var annualRainfallStats = ee.FeatureCollection(annualRainfallFeatures);

// Mencetak hasil
print('3. Curah Hujan Tahunan dan Faktor Erosivitas R:', annualRainfallStats);

// =======================================================================
// 3. Membuat Grafik Batang untuk Total Curah Hujan Tahunan
// =======================================================================
var annualRainfallStatsClean = annualRainfallStats.map(function(f) {
  var year = ee.Number(f.get('year'));
  return f.set('year_label', year.format('%d'));
});

// Grafik Batang untuk Total Curah Hujan Tahunan
var annualRainfallBarChart = ui.Chart.feature.byFeature({
  features: annualRainfallStatsClean,
  xProperty: 'year_label',
  yProperties: ['totalRainfall']
})
.setChartType('ColumnChart')
.setOptions({
  title: 'Total Curah Hujan Tahunan (2015–2024) - CHIRPS (Akumulasi ROI)',
  vAxis: {
    title: 'Curah Hujan (MJ·mm·ha⁻¹·h⁻¹·tahun⁻¹)',
    gridlines: {count: 10},
    minValue: 0
  },
  hAxis: {
    title: 'Tahun'
  },
  colors: ['darkblue'], // biru
  legend: {position: 'none'},
  bar: {groupWidth: '70%'}
});
print(annualRainfallBarChart);

// =======================================================================
// 4. Membuat Grafik Sebar dengan Garis Tren untuk Faktor Erosivitas R
// =======================================================================
var erosivityFactorChart = ui.Chart.feature.byFeature({
  features: annualRainfallStatsClean,
  xProperty: 'year',
  yProperties: ['erosivityFactorR']
})
.setChartType('ScatterChart')
.setOptions({
  title: 'Total Curah Hujan Tahunan (2015–2024) - CHIRPS (Akumulasi ROI)',
  hAxis: {
    title: 'Tahun',
    format: '####'
  },
  vAxis: {
    title: 'Curah Hujan (MJ·mm·ha⁻¹·h⁻¹·tahun⁻¹)'
  },
  pointSize: 6,
  legend: {position: 'bottom'},
  series: {
    0: {
      labelInLegend: ' Curah Hujan (Faktor Erosivitas R)',
      color: '#ff7f0e',
      pointShape: 'circle'
    }
  },
  trendlines: {
    0: {
      type: 'linear',
      color: 'red',
      lineWidth: 2,
      showR2: true,
      visibleInLegend: true,
      labelInLegend: 'Garis Tren'
    }
  }
});
print(erosivityFactorChart);

// =======================================================================
//  5. Kondisi rata-rata pertahun Curah Hujan --->> sum() to mean()
// =======================================================================
var annualRainfallImages = years.map(function(year) {
  year = ee.Number(year);
  // Menjumlahkan curah hujan harian untuk tahun yang diberikan
  var annualSum = chirps
                  .filter(ee.Filter.calendarRange(year, year, 'year'))
                  .sum()
                  .clip(roi)
                  .set('year', year);
  return annualSum;
});

var annualRainfallCollection = ee.ImageCollection.fromImages(annualRainfallImages);

// Menghitung total curah hujan tahunan untuk setiap tahun dan menghitung R
var annualRainfallFeatures = annualRainfallCollection.map(function(image) {
  var total = image.reduceRegion({
    reducer: ee.Reducer.mean(),
    geometry: roi,
    scale: 1000,
    maxPixels: 1e13,
    bestEffort: true
  }).get('precipitation');

  // Menghitung R menggunakan rumus R = 0.5 * P
  var R = ee.Number(total).multiply(0.5);

  return ee.Feature(null, {
    year: image.get('year'),
    totalRainfall: total,
    erosivityFactorR: R
  });
});

// Mengonversi ke FeatureCollection
var annualRainfallStats = ee.FeatureCollection(annualRainfallFeatures);

// Mencetak hasil
print('4. Curah Hujan rata-rata pertahun', annualRainfallStats);

// =======================================================================
// 6. Membuat Grafik Batang untuk Total Curah Hujan Tahunan
// =======================================================================
var annualRainfallStatsClean = annualRainfallStats.map(function(f) {
  var year = ee.Number(f.get('year'));
  return f.set('year_label', year.format('%d'));
});

// Grafik Batang untuk Total Curah Hujan Tahunan
var annualRainfallBarChart = ui.Chart.feature.byFeature({
  features: annualRainfallStatsClean,
  xProperty: 'year_label',
  yProperties: ['totalRainfall']
})
.setChartType('ColumnChart')
.setOptions({
  title: 'Total Curah Hujan Rata-Rata per Tahun (2015–2024) - CHIRPS',
  vAxis: {
    title: 'Curah Hujan (mm/tahun)',
    gridlines: {count: 10},
    minValue: 0
  },
  hAxis: {
    title: 'Tahun'
  },
  colors: ['darkblue'], // biru
  legend: {position: 'none'},
  bar: {groupWidth: '70%'}
});
print(annualRainfallBarChart);

// =======================================================================
// 7. Membuat Grafik Sebar dengan Garis Tren untuk Faktor Erosivitas R
// =======================================================================
var erosivityFactorChart = ui.Chart.feature.byFeature({
  features: annualRainfallStatsClean,
  xProperty: 'year',
  yProperties: ['erosivityFactorR']
})
.setChartType('ScatterChart')
.setOptions({
  title: 'Total Curah Hujan Rata-Rata per Tahun (2015–2024) - CHIRPS',
  hAxis: {
    title: 'Tahun',
    format: '####'
  },
  vAxis: {
    title: 'Curah Hujan (mm/bulan)'
  },
  pointSize: 6,
  legend: {position: 'bottom'},
  series: {
    0: {
      labelInLegend: ' Curah Hujan',
      color: '#ff7f0e',
      pointShape: 'circle'
    }
  },
  trendlines: {
    0: {
      type: 'linear',
      color: 'red',
      lineWidth: 2,
      showR2: true,
      visibleInLegend: true,
      labelInLegend: 'Garis Tren'
    }
  }
});
print(erosivityFactorChart);

// =======================================================================
// 8. Kondisi rata-rata pertahun Curah Hujan --->> sum() to mean()
// =======================================================================
var annualRainfallImages = years.map(function(year) {
  year = ee.Number(year);
  // Menjumlahkan curah hujan harian untuk tahun yang diberikan
  var annualSum = chirps
                  .filter(ee.Filter.calendarRange(year, year, 'year'))
                  .mean()
                  .clip(roi)
                  .set('year', year);
  return annualSum;
});

var annualRainfallCollection = ee.ImageCollection.fromImages(annualRainfallImages);

// Menghitung total curah hujan tahunan untuk setiap tahun dan menghitung R
var annualRainfallFeatures = annualRainfallCollection.map(function(image) {
  var total = image.reduceRegion({
    reducer: ee.Reducer.mean(),
    geometry: roi,
    scale: 1000,
    maxPixels: 1e13,
    bestEffort: true
  }).get('precipitation');

  // Menghitung R menggunakan rumus R = 0.5 * P
  var R = ee.Number(total).multiply(0.5);

  return ee.Feature(null, {
    year: image.get('year'),
    totalRainfall: total,
    erosivityFactorR: R
  });
});

// Mengonversi ke FeatureCollection
var annualRainfallStats = ee.FeatureCollection(annualRainfallFeatures);

// Mencetak hasil
print('5. Curah Hujan rata-rata pertahun', annualRainfallStats);

// =======================================================================
// 9. Membuat Grafik Batang untuk Total Curah Hujan Tahunan
// =======================================================================
var annualRainfallStatsClean = annualRainfallStats.map(function(f) {
  var year = ee.Number(f.get('year'));
  return f.set('year_label', year.format('%d'));
});

// Grafik Batang untuk Total Curah Hujan Tahunan
var annualRainfallBarChart = ui.Chart.feature.byFeature({
  features: annualRainfallStatsClean,
  xProperty: 'year_label',
  yProperties: ['totalRainfall']
})
.setChartType('ColumnChart')
.setOptions({
  title: 'Curah Hujan Rata-Rata Harian per Tahun (2015–2024) - CHIRPS',
  vAxis: {
    title: 'Curah Hujan (mm/tahun)',
    gridlines: {count: 10},
    minValue: 0
  },
  hAxis: {
    title: 'Tahun'
  },
  colors: ['darkblue'], // biru
  legend: {position: 'none'},
  bar: {groupWidth: '70%'}
});
print(annualRainfallBarChart);

// =======================================================================
// 10. Membuat Grafik Sebar dengan Garis Tren untuk Faktor Erosivitas R
// =======================================================================
var erosivityFactorChart = ui.Chart.feature.byFeature({
  features: annualRainfallStatsClean,
  xProperty: 'year',
  yProperties: ['erosivityFactorR']
})
.setChartType('ScatterChart')
.setOptions({
  title: 'Curah Hujan Rata-Rata Harian per Tahun (2015–2024) - CHIRPS',
  hAxis: {
    title: 'Tahun',
    format: '####'
  },
  vAxis: {
    title: 'Curah Hujan (mm/hari)'
  },
  pointSize: 6,
  legend: {position: 'bottom'},
  series: {
    0: {
      labelInLegend: ' Curah Hujan',
      color: '#ff7f0e',
      pointShape: 'circle'
    }
  },
  trendlines: {
    0: {
      type: 'linear',
      color: 'red',
      lineWidth: 2,
      showR2: true,
      visibleInLegend: true,
      labelInLegend: 'Garis Tren'
    }
  }
});
print(erosivityFactorChart);

// =======================================================================
// 11. Menghitung Rata-rata Curah Hujan Bulanan (2015-2024)
// =======================================================================
var monthlyRainfallImages = months.map(function(month) {
  month = ee.Number(month);
  // Menjumlahkan curah hujan harian untuk bulan yang diberikan
  var monthlySum = chirps
                    .filter(ee.Filter.calendarRange(month, month, 'month'))
                    .sum()
                    .clip(roi)
                    .set('month', month);
  return monthlySum;
});

var monthlyRainfallCollection = ee.ImageCollection.fromImages(monthlyRainfallImages);

// Menghitung rata-rata curah hujan bulanan untuk setiap bulan
var monthlyRainfallFeatures = monthlyRainfallCollection.map(function(image) {
  var total = image.reduceRegion({
    reducer: ee.Reducer.mean(),
    geometry: roi,
    scale: 1000,
    maxPixels: 1e13,
    bestEffort: true
  }).get('precipitation');

  return ee.Feature(null, {
    month: image.get('month'),
    totalRainfall: total
  });
});

// Mengonversi ke FeatureCollection
var monthlyRainfallStats = ee.FeatureCollection(monthlyRainfallFeatures);

// Mencetak hasil
print('6. Rata-rata Curah Hujan Bulanan (mm):', monthlyRainfallStats);

// =======================================================================
// 12. Membuat Grafik Batang untuk Rata-rata Curah Hujan Bulanan
// =======================================================================
var monthlyRainfallStatsClean = monthlyRainfallStats.map(function(f) {
  var month = ee.Number(f.get('month'));
  return f.set('month_label', monthNames.get(month.subtract(1))); // Mengambil nama bulan
});

// Grafik Batang untuk Rata-rata Curah Hujan Bulanan
var monthlyRainfallBarChart = ui.Chart.feature.byFeature({
  features: monthlyRainfallStatsClean,
  xProperty: 'month_label',
  yProperties: ['totalRainfall']
})
.setChartType('ColumnChart')
.setOptions({
  title: 'Rata-rata Curah Hujan Bulanan (2015–2024) - CHIRPS',
  vAxis: {
    title: 'Curah Hujan (mm/bulan)',
    gridlines: {count: 10},
    minValue: 0
  },
  hAxis: {
    title: 'Bulan'
  },
  colors: ['darkblue'], // biru
  legend: {position: 'none'},
  bar: {groupWidth: '70%'}
});
print(monthlyRainfallBarChart);

// =======================================================================
// 13. Membuat Grafik Sebar dengan Garis Tren untuk Rata-rata Curah Hujan Bulanan
// =======================================================================
var monthlyErosivityFactorChart = ui.Chart.feature.byFeature({
  features: monthlyRainfallStatsClean,
  xProperty: 'month',
  yProperties: ['totalRainfall']
})
.setChartType('ScatterChart')
.setOptions({
  title: 'Rata-rata Curah Hujan Bulanan (2015–2024) - CHIRPS',
  hAxis: {
    title: 'Bulan',
    format: '####'
  },
  vAxis: {
    title: 'Curah Hujan (mm/bulan)'
  },
  pointSize: 6,
  legend: {position: 'bottom'},
  series: {
    0: {
      labelInLegend: ' Curah Hujan',
      color: '#ff7f0e',
      pointShape: 'circle'
    }
  },
  trendlines: {
    0: {
      type: 'linear',
      color: 'red',
      lineWidth: 2,
      showR2: true,
      visibleInLegend: true,
      labelInLegend: 'Garis Tren'
    }
  }
});
print(monthlyErosivityFactorChart);

// =======================================================================
// 14. Menghitung Rata-rata Curah Hujan dari Bulan yang Sama (2015-2024)
// =======================================================================
var monthlyAverageImages = months.map(function(month) {
  month = ee.Number(month);
  // Hitung rata-rata curah hujan dari bulan yang sama (misal Januari dari setiap tahun)
  var monthlyAvg = chirps
                    .filter(ee.Filter.calendarRange(month, month, 'month'))
                    .filter(ee.Filter.calendarRange(2015, 2024, 'year'))
                    .mean()  // Rata-rata curah hujan untuk bulan ini selama 2015-2024
                    .clip(roi)
                    .set('month', month);
  return monthlyAvg;
});

var monthlyAverageCollection = ee.ImageCollection.fromImages(monthlyAverageImages);

// Menghitung rata-rata curah hujan per bulan dari semua tahun (nilai rata-rata spasial)
var monthlyAverageFeatures = monthlyAverageCollection.map(function(image) {
  var mean = image.reduceRegion({
    reducer: ee.Reducer.mean(),
    geometry: roi,
    scale: 1000,
    maxPixels: 1e13,
    bestEffort: true
  }).get('precipitation');

  return ee.Feature(null, {
    month: image.get('month'),
    meanRainfall: mean
  });
});

// Konversi ke FeatureCollection
var monthlyAverageStats = ee.FeatureCollection(monthlyAverageFeatures);

// Tambahkan label nama bulan berdasarkan nomor bulan
var monthlyAverageStatsClean = monthlyAverageStats.map(function(feature) {
  var monthIndex = ee.Number(feature.get('month')).toInt().subtract(1);
  var monthName = monthNames.get(monthIndex);
  return feature.set('month_label', monthName);
});

// Tampilkan hasil tabel di konsol
print('Rata-rata Curah Hujan dari Bulan yang Sama (mm) 2015-2024:', monthlyAverageStatsClean);

// Buat grafik batang
var monthlyAverageBarChart = ui.Chart.feature.byFeature({
  features: monthlyAverageStatsClean,
  xProperty: 'month_label',
  yProperties: ['meanRainfall']
})
.setChartType('ColumnChart')
.setOptions({
  title: 'Rata-rata Curah Hujan dari Bulan yang Sama (2015–2024)',
  vAxis: {
    title: 'Curah Hujan (mm)',
    gridlines: {count: 10},
    minValue: 0
  },
  hAxis: {
    title: 'Bulan'
  },
  colors: ['#00796B'], // warna hijau tua
  legend: {position: 'none'},
  bar: {groupWidth: '70%'}
});
print(monthlyAverageBarChart);

// Buat grafik sebar dengan garis tren
var monthlyAverageScatterChart = ui.Chart.feature.byFeature({
  features: monthlyAverageStatsClean,
  xProperty: 'month',
  yProperties: ['meanRainfall']
})
.setChartType('ScatterChart')
.setOptions({
  title: 'Tren Rata-rata Curah Hujan dari Bulan yang Sama (2015–2024)',
  hAxis: {
    title: 'Bulan'
  },
  vAxis: {
    title: 'Curah Hujan (mm)'
  },
  pointSize: 6,
  colors: ['#00796B'],
  trendlines: {0: {
    type: 'linear',
    color: '#D32F2F',
    lineWidth: 2,
    opacity: 0.7,
    showR2: true,
    visibleInLegend: true,
    labelInLegend: 'Garis Tren'
  }},
  legend: {position: 'bottom'}
});
print(monthlyAverageScatterChart);

```

__________________________ 
_

### References
.....



________________________________________________________________________________________________________________________________________________________
**`Abd. Malik A. Madinu`**
<br /> _Department of Geophysics and Meteorology, Faculty of Mathematics and Natural Sciences, IPB University_
<br /> _SSRS Fellow RO11 Dynamic Climate Research Group, IPB Sustainable Science Research Students Association (IPB SSRS Association), IPB University_
<br /> _SSRS Indonesia Tropic and Ecological Observatory, IPB Sustainable Science Research Students Association (IPB SSRS Association), SSRS Group_
<br /> malikzone314@gmail.com   abd.malik@apps.ipb.ac.id
<br /> [![Email](https://img.shields.io/badge/Email-D14836?style=flat&logo=gmail&logoColor=white)](mailto:Malikzone314@gmail.com)
