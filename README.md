# HW-Project-5
#include <iostream>
#include <fstream>
#include <string>
#include <vector>
#include <algorithm>
#include <sstream>

using namespace std;

// Function to split a string into a vector of strings
vector<string> split(string str, char delimiter) {
    vector<string> tokens;
    stringstream ss(str);
    string token;
    while (getline(ss, token, delimiter)) {
        tokens.push_back(token);
    }
    return tokens;
}

// Function to remove duplicates from a vector of strings
void remove_duplicates(vector<string>& vec) {
    sort(vec.begin(), vec.end());
    vec.erase(unique(vec.begin(), vec.end()), vec.end());
}

// Function to count unique and total visitors for a website
void count_visitors(vector<string> ips, int& total_visitors, int& unique_visitors) {
    total_visitors = ips.size();
    remove_duplicates(ips);
    unique_visitors = ips.size();
}

// Function to process a log file for a single website
void process_log_file(string filename, string output_filename) {
    ifstream infile(filename);
    ofstream outfile(output_filename);
    if (!infile) {
        cerr << "Error: could not open input file " << filename << endl;
        exit(1);
    }
    if (!outfile) {
        cerr << "Error: could not open output file " << output_filename << endl;
        exit(1);
    }
    string line;
    int total_visitors, unique_visitors;
    vector<string> ips;
    while (getline(infile, line)) {
        if (line.empty()) {
            continue;
        }
        if (line == "-----") {
            count_visitors(ips, total_visitors, unique_visitors);
            outfile << "Total visitors: " << total_visitors << endl;
            outfile << "Unique visitors: " << unique_visitors << endl;
            for (string ip : ips) {
                outfile << ip << endl;
            }
            outfile << "-----" << endl;
            ips.clear();
        } else {
            vector<string> tokens = split(line, ' ');
            ips.insert(ips.end(), tokens.begin(), tokens.end());
        }
    }
    // Process the last website in the file
    count_visitors(ips, total_visitors, unique_visitors);
    outfile << "Total visitors: " << total_visitors << endl;
    outfile << "Unique visitors: " << unique_visitors << endl;
    for (string ip : ips) {
        outfile << ip << endl;
    }
    outfile << "-----" << endl;
}

int main(int argc, char** argv) {
    if (argc != 3) {
        cerr << "Usage: " << argv[0] << " input_file output_file" << endl;
        exit(1);
    }
    string input_filename = argv[1];
    string output_filename = argv[2];
    process_log_file(input_filename, output_filename);
    return 0;
}
