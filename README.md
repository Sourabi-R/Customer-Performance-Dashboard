<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Customer Performance Dashboard</title>
    <script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
        }

        #dashboard {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-around;
            margin-top: 20px;
        }

        .chart {
            width: 45%;
            margin: 20px 0;
        }

        #upload {
            margin: 20px;
            text-align: center;
        }

        input[type="file"] {
            margin: 10px 0;
        }
    </style>
</head>
<body>
    <div id="upload">
        <h2>Upload Your JSON Sales Data</h2>
        <input type="file" id="fileInput" accept="application/json">
    </div>

    <div id="dashboard">
        <div id="salesByBrand" class="chart"></div>
        <div id="priceDistribution" class="chart"></div>
        <div id="rangeEfficiency" class="chart"></div>
        <div id="preferencesBySegment" class="chart"></div>
        <div id="fastChargeVsPrice" class="chart"></div>
    </div>

    <script>
        document.getElementById('fileInput').addEventListener('change', function(event) {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    try {
                        const data = JSON.parse(e.target.result);
                        createDashboard(data);
                    } catch (error) {
                        alert('Invalid JSON file. Please upload a valid file.');
                    }
                };
                reader.readAsText(file);
            }
        });

        function createDashboard(data) {
           
            const brands = data.map(item => item.Brand);
            const brandCounts = {};
            brands.forEach(brand => {
                brandCounts[brand] = (brandCounts[brand] || 0) + 1;
            });

            const brandNames = Object.keys(brandCounts);
            const brandSales = Object.values(brandCounts);

            Plotly.newPlot('salesByBrand', [{
                x: brandNames,
                y: brandSales,
                type: 'bar',
                marker: { color: 'teal' }
            }], {
                title: 'Sales by Brand',
                xaxis: { title: 'Brand' },
                yaxis: { title: 'Number of Sales' }
            });

         
            const prices = data.map(item => item.PriceEuro);

            Plotly.newPlot('priceDistribution', [{
                x: prices,
                type: 'histogram',
                marker: { color: 'purple' }
            }], {
                title: 'Price Distribution',
                xaxis: { title: 'Price (Euro)' },
                yaxis: { title: 'Frequency' }
            });

           
            const ranges = data.map(item => item.Range_Km);
            const efficiencies = data.map(item => item.Efficiency_WhKm);

            Plotly.newPlot('rangeEfficiency', [{
                x: efficiencies,
                y: ranges,
                mode: 'markers',
                type: 'scatter',
                marker: { size: 10, color: 'orange' }
            }], {
                title: 'Range vs. Efficiency',
                xaxis: { title: 'Efficiency (Wh/Km)' },
                yaxis: { title: 'Range (Km)' }
            });

            
            const segments = data.map(item => item.Segment);
            const bodyStyles = data.map(item => item.BodyStyle);

            const segmentCounts = {};
            segments.forEach(segment => {
                segmentCounts[segment] = (segmentCounts[segment] || 0) + 1;
            });

            const bodyStyleCounts = {};
            bodyStyles.forEach(style => {
                bodyStyleCounts[style] = (bodyStyleCounts[style] || 0) + 1;
            });

            Plotly.newPlot('preferencesBySegment', [{
                x: Object.keys(segmentCounts),
                y: Object.values(segmentCounts),
                type: 'bar',
                name: 'Segment',
                marker: { color: 'blue' }
            }, {
                x: Object.keys(bodyStyleCounts),
                y: Object.values(bodyStyleCounts),
                type: 'bar',
                name: 'Body Style',
                marker: { color: 'green' }
            }], {
                title: 'Customer Preferences by Segment and Body Style',
                barmode: 'group',
                xaxis: { title: 'Category' },
                yaxis: { title: 'Count' }
            });

           
            const fastCharge = data.map(item => item.FastCharge_KmH);

            Plotly.newPlot('fastChargeVsPrice', [{
                x: prices,
                y: fastCharge,
                mode: 'markers',
                marker: { size: 10, color: 'red' }
            }], {
                title: 'Fast Charge Capability vs. Price',
                xaxis: { title: 'Price (Euro)' },
                yaxis: { title: 'Fast Charge (Km/H)' }
            });
        }
    </script>
</body>
</html>
