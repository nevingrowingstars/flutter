      // Pseudocode for FreeDraw sync with WebRTC or WebSocket
      
      class FreeDrawSync {
        final WebRTCConnection connection;
        List<Offset> pointsBatch = [];
        final int batchSize = 10; // Number of points per batch
        final int sampleRate = 50; // Time in ms to send data
      
        FreeDrawSync(this.connection);
      
        void onDraw(Offset point) {
          // Add the new point to the batch
          pointsBatch.add(point);
      
          // If batch size is reached, send data
          if (pointsBatch.length >= batchSize) {
            sendBatch();
            pointsBatch.clear();
          }
        }
      
        void sendBatch() {
          final data = {
            "type": "freedraw",
            "points": pointsBatch.map((p) => {"x": p.dx, "y": p.dy}).toList(),
            "color": "#FF0000",
            "strokeWidth": 3
          };
          connection.sendData(jsonEncode(data));
        }
      
        void endDraw() {
          // Send remaining points in batch and clear
          if (pointsBatch.isNotEmpty) {
            sendBatch();
            pointsBatch.clear();
          }
        }
      
        void onDataReceived(String data) {
          final Map<String, dynamic> jsonData = jsonDecode(data);
      
          if (jsonData["type"] == "freedraw") {
            final List<Offset> points = (jsonData["points"] as List)
                .map((p) => Offset(p["x"], p["y"]))
                .toList();
            drawReceivedPoints(points, jsonData["color"], jsonData["strokeWidth"]);
          }
        }
      
        void drawReceivedPoints(List<Offset> points, String color, double strokeWidth) {
          // Function to draw points on canvas
          // Draw using color and stroke width provided
        }
      }
