int[] startTimes = {22, 23, 21, 0, 23, 22, 21, 2, 1, 22, 23, 21, 23, 22}; // Sleep start times
int[] endTimes = {6, 7, 5, 8, 6, 7, 6, 8, 5, 6, 7, 6, 7, 8};              // Sleep end times
String[] days = {
  "Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun", 
  "Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"
}; // Days of the week
String[] feelings = {
  "Energetic", "Tired", "Groggy", "Relaxed", "Happy", "Drowsy", "Focused",
  "Exhausted", "Grumpy", "Energetic", "Neutral", "Happy", "Focused", "Excited"
}; // Feelings for each day

int margin = 50;       // Space around the graph
int graphHeight = 300; // Height of the bars
int barWidth = 60;     // Width of each bar
int yOffset = margin;  // Top margin

void setup() {
   size(1200, 600); // Adjusted canvas size
   noLoop(); // Stop continuous drawing
   drawBackground();
   drawGraph();
   displayTotals();
}

void drawBackground() {
   // Gradient background
   for (int y = 0; y < height; y++) {
       float inter = map(y, 0, height, 0, 1);
       int c = lerpColor(color(135, 206, 250), color(240, 248, 255), inter); // Sky blue to white
       stroke(c);
       line(0, y, width, y);
   }
}

void drawGraph() {
   for (int i = 0; i < startTimes.length; i++) {
       // Calculate sleep duration
       int sleepStart = startTimes[i];
       int sleepEnd = endTimes[i];
       int duration = (sleepEnd >= sleepStart) ? (sleepEnd - sleepStart) : (24 - sleepStart + sleepEnd);

       // Determine bar color based on sleep duration
       if (duration < 6) {
           fill(255, 0, 0); // Red for less than 6 hours
       } else if (duration >= 6 && duration < 8) {
           fill(255, 255, 0); // Yellow for 6–7.9 hours
       } else {
           fill(0, 255, 0); // Green for 8+ hours
       }

       // Calculate rectangle dimensions
       float x = margin + i * barWidth + barWidth / 2;
       float barHeight = map(duration, 0, 12, 0, graphHeight); // Scale duration to graph height

       // Draw sleep bar
       rect(x - barWidth / 4, yOffset + graphHeight - barHeight, barWidth / 2, barHeight);

       // Add day label
       fill(0);
       textAlign(CENTER);
       textSize(12);
       text(days[i], x, yOffset + graphHeight + 30);

       // Add duration label below the bar
       text(duration + " hrs", x, yOffset + graphHeight + 50);

       // Draw separator for weeks
       if (i == 6) { // Separator after Week 1
           stroke(0);
           line(x + barWidth / 2, yOffset, x + barWidth / 2, yOffset + graphHeight + 70);
           noStroke();
       }
   }
}

void displayTotals() {
   int week1Total = 0, week2Total = 0;

   // Calculate total sleep for Week 1
   for (int i = 0; i < 7; i++) {
       week1Total += (endTimes[i] >= startTimes[i]) ? (endTimes[i] - startTimes[i]) : (24 - startTimes[i] + endTimes[i]);
   }

   // Calculate total sleep for Week 2
   for (int i = 7; i < startTimes.length; i++) {
       week2Total += (endTimes[i] >= startTimes[i]) ? (endTimes[i] - startTimes[i]) : (24 - startTimes[i] + endTimes[i]);
   }

   // Display totals
   fill(0);
   textAlign(CENTER);
   textSize(16);
   text("Week 1 Total: " + week1Total + " hrs", width / 3, height - 50);
   text("Week 2 Total: " + week2Total + " hrs", 2 * width / 3, height - 50);
   text("Combined Total: " + (week1Total + week2Total) + " hrs", width / 2, height - 20);
}

void mousePressed() {
   // Detect which bar is clicked
   for (int i = 0; i < startTimes.length; i++) {
       float x = margin + i * barWidth + barWidth / 2;
       float barHeight = map((endTimes[i] >= startTimes[i]) ? (endTimes[i] - startTimes[i]) : (24 - startTimes[i] + endTimes[i]), 0, 12, 0, graphHeight);

       float barTop = yOffset + graphHeight - barHeight;
       float barBottom = yOffset + graphHeight;

       // Check if mouse is within bar bounds
       if (mouseX > x - barWidth / 4 && mouseX < x + barWidth / 4 && mouseY > barTop && mouseY < barBottom) {
           // Print details to the console
           println("Day: " + days[i]);
           println("Sleep Duration: " + ((endTimes[i] >= startTimes[i]) ? (endTimes[i] - startTimes[i]) : (24 - startTimes[i] + endTimes[i])) + " hrs");
           println("Start Time: " + startTimes[i] + ":00");
           println("End Time: " + endTimes[i] + ":00");
           println("Feeling: " + feelings[i]);
           println("-----");
           return;
       }
   }
}
