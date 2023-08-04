# SubtitleSyncronization

#include <iostream>
#include <fstream>
#include <sstream>
#include <string>

// Function to add or subtract time from a timestamp in the format "HH:MM:SS,mmm"
std::string adjustTime(const std::string& timestamp, int timeAdjust) {
  int hours, minutes, seconds, milliseconds;
  char colon;
  std::stringstream ss(timestamp);
  ss >> hours >> colon >> minutes >> colon >> seconds >> colon >> milliseconds;
  milliseconds += timeAdjust;
  seconds += milliseconds / 1000;
  milliseconds %= 1000;
  minutes += seconds / 60;
  seconds %= 60;
  hours += minutes / 60;
  minutes %= 60;
  std::stringstream result;
  result << hours << ':' << minutes << ':' << seconds << ',' << milliseconds;
  return result.str();
}

int main() {
  // Open the input file
  std::ifstream input("Interstellar.srt");
  if (!input.is_open()) {
    std::cerr << "Error: Unable to open input file\n";
    return 1;
  }

  // Read the time adjustment value from the user
  int timeAdjust;
  std::cout << "Enter time adjustment (in milliseconds): ";
  std::cin >> timeAdjust;
// Create the output file
  std::ofstream output("modified.srt");
  // Process the file line by line
  std::string line;
  while (std::getline(input, line)) {
    // Check if the line is a timestamp
    std::size_t found = line.find(" --> ");
    if (found != std::string::npos) {
      // Split the line into the start and end timestamps
      std::string start = line.substr(0, found);
      std::string end = line.substr(found + 5);
      // Adjust the timestamps
      start = adjustTime(start, timeAdjust);
      end = adjustTime(end, timeAdjust);

      output << start << " --> " << end << '\n';
      } else {
      // Print the line as-is
      output << line << '\n';
    }
  }

  return 0;
}
