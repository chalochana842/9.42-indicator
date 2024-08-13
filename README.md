/@version=5
indicator("9:45 AM Line - Last 20 Days (UTC-4)", overlay=true)

// Input parameters
line_color = input.color(color.red, "Line Color")
line_width = input.int(2, "Line Width")
days_to_show = input.int(20, "Number of Days")

// Define arrays to store lines and times
var float[] levels = array.new_float()
var int[] times = array.new_int()

// Function to calculate timestamp for a specific time
get_target_timestamp(year, month, dayofmonth, hour, minute) =>
    timestamp("UTC-4", year, month, dayofmonth, hour, minute)

// Get the current time and date adjusted to the UTC-4 timezone
current_time = timestamp("UTC-4", year, month, dayofmonth, hour, minute)

// Define the target times (9:45 AM and 9:42 AM) in the UTC-4 time zone
target_hour_945 = 9
target_minute_945 = 45
target_hour_942 = 9
target_minute_942 = 42

// Check if the current time matches 9:45 AM
if (hour(current_time) == target_hour_945 and minute(current_time) == target_minute_945)
    // Store the high of the 9:45 AM candle and the current bar index
    array.unshift(levels, high)
    array.unshift(times, bar_index)
    // Remove the oldest level if the array exceeds the number of days
    if (array.size(levels) > days_to_show)
        array.pop(levels)
        array.pop(times)

// Draw or update the 9:45 AM lines
if (array.size(levels) > 0)
    for i = 0 to array.size(levels) - 1
        // Ensure lines do not extend beyond 500 bars into the future
        line_end = math.min(bar_index + 500, bar_index + 500)
        line.new(x1=array.get(times, i), y1=array.get(levels, i), x2=line_end, y2=array.get(levels, i), color=line_color, width=line_width, extend=extend.none)

// Check if the current bar is at 9:42 AM and draw the line
if (hour(current_time) == target_hour_942 and minute(current_time) == target_minute_942)
    line.new(x1=bar_index, y1=open, x2=bar_index + 1, y2=open, color=color.blue, width=1, extend=extend.right)

// Plot to ensure the script runs smoothly
plot(close, color=color.blue)
