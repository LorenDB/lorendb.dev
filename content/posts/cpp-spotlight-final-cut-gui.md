+++
title = 'C++ Spotlight: FINAL CUT terminal GUI'
description = "Yes, the library name is capitalized."
date = 2024-03-02T15:20:00-05:00
tags = ["c++", "finalcut", "programming", "c++ spotlight", "terminal"]
images = ["images/final-cut/finaltop-all-dialogs.png"]
+++

This is the first post in what will hopefully be a long series where I take a look at different C++ libraries and utilities that are uniquely awesome. Today, we're looking at [FINAL CUT](https://github.com/gansm/finalcut), a library for implementing a GUI in the terminal. It promises an API that is very similar to Qt's API and plenty of functionality, and it doesn't disappoint.

## Getting started

I was happy to see that openSUSE packages FINAL CUT. A quick `sudo zypper in libfinal-devel` later, I had the library installed on my system. Getting it into my C++ test project, however, was not quite as fun. It appears that FINAL CUT doesn't provide a CMake interface out of the box, so I had to manually add the library to my CMake file:

``` cmake
cmake_minimum_required(VERSION 3.5)

project(cpp-playground LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_library(final final)

add_executable(cpp-playground main.cpp)
target_link_libraries(cpp-playground PRIVATE final)
```

It's not a huge dealbreaker or anything, but it's always nice to be able to use `find_package` instead of `find_library`, given that `find_package` does so much more.

With CMake set up, let's try the basic dialog example linked in the FINAL CUT wiki:

``` cpp
#include <final/final.h>

int main(int argc, char *argv[])
{
    finalcut::FApplication app(argc, argv);
    finalcut::FDialog dialog(&app);
    dialog.setText("A dialog");
    const finalcut::FPoint position{25, 5};
    const finalcut::FSize size{30, 10};
    dialog.setGeometry(position, size);
    finalcut::FWidget::setMainWidget(&dialog);
    dialog.show();
    return app.exec();
}
```

Running that code will show a window in your terminal; you can drag it around with your mouse and click on the button in the titlebar to open the menu!

{{< figure
    src="https://lorendb.dev/images/final-cut/dialog-example.png"
    alt="A basic dialog made with FINAL CUT"
    title="That background would make my eyes hurt if it stayed there too long..."
>}}

## Let's build something useful!

Demos are great, but what if we could build something that has an actual use? Let's build a proof-of-concept system monitor application that will show individual resources in separate windows. We'll start with a main dialog that shows the app name and version:

``` cpp
class MainDialog : public finalcut::FDialog
{
public:
    MainDialog(finalcut::FApplication *app)
        : finalcut::FDialog(app)
    {
        setText("finaltop");
        const finalcut::FPoint position{4, 2};
        const finalcut::FSize size{17, 3};

        m_about = new finalcut::FLabel("finaltop v0.0.1", this);
        m_about->setGeometry({3, 2}, {20, 5});

        setGeometry(position, size);
    }

private:
    finalcut::FLabel *m_about;
};
```

For reference, our `main()` function looks like this:

``` cpp
int main(int argc, char *argv[])
{
    finalcut::FApplication app(argc, argv);

    MainDialog mainDialog(&app);
    mainDialog.show();
    finalcut::FWidget::setMainWidget(&mainDialog);

    return app.exec();
}
```

Note the call to `finalcut::FWidget::setMainWidget()`; this marks our MainDialog as the primary dialog for the application. Closing this dialog will shut down the entire app.

Now let's get down to business and implement individual resource dialogs, beginning with adding an uptime counter to the main dialog. Since Linux adheres to the ["everything is a file"](https://en.wikipedia.org/wiki/Everything_is_a_file) design, we can easily parse this from `/proc/uptime`:

``` cpp
std::string getUptime()
{
    std::ifstream proc_uptime("/proc/uptime");
    double uptime;
    proc_uptime >> uptime;
    int hours, minutes, seconds;
    hours = static_cast<int>(uptime / 3600);
    minutes = static_cast<int>((uptime - hours * 3600) / 60);
    seconds = static_cast<int>(uptime - hours * 3600 - minutes * 60);
    return std::to_string(hours) + "h " + std::to_string(minutes) + "m " + std::to_string(seconds) + "s";
}
```

We could dump this string directly to the dialog in the constructor, but that's no fun. Let's utilize timers to update a label every second. First, we need to create a label and register a timer for it in the constructor:

``` cpp
// m_uptime is a finalcut::FLabel * and m_uptimeTimer is an int
m_uptime = new finalcut::FLabel("Uptime: " + getUptime(), this);
m_uptime->setGeometry({3, 2}, {30, 1});
m_uptimeTimer = addTimer(1000);
```

Next, we'll add a function to `MainDialog` that will handle the timer:

``` cpp
void onTimer(finalcut::FTimerEvent *e) override
{
    if (e->getTimerId() == m_uptimeTimer)
    {
        m_uptime->setText("Uptime: " + getUptime());
        // Note that FINAL CUT will not automatically redraw controls, likely
        // because rendering to the terminal is a pretty expensive operation.
        m_uptime->redraw();
    }
}
```

With that done, we now have a nifty dynamic uptime counter! Here's what it looks like on my machine:

{{< figure
    src="https://lorendb.dev/images/final-cut/finaltop-main-dialog.png"
    alt="A dialog displaying 'finaltop v0.0.1' along with system uptime information"
>}}

Next, we'll build a CPU meter. For sake of brevity and simplicity, I won't implement a per-core monitor; instead, I'll just use the first line of `/proc/stat` to get overall CPU usage. With that being said, the logic to extend CPU monitoring to cover all cores would be simple; while the first line of `/proc/stat` is the overall CPU usage, the next number of lines provide per-core monitoring, so we could reuse the parsing logic to extract the per-core usage values and create a dynamically-sized list of progress bars to monitor each of them.

First, we'll add a CPU widget class that displays a `finalcut::FProgressBar`, as well as the CPU name from `/proc/cpuinfo`:

``` cpp
class CpuDialog : public finalcut::FDialog
{
public:
    CpuDialog(finalcut::FWidget *parent)
        : finalcut::FDialog(parent)
    {
        setText("CPU");
        setGeometry({4, 9}, {60, 7});

        m_cpuMeter = new finalcut::FProgressbar{this};
        m_cpuMeter->setGeometry({3, 2}, {50, 1});
        parseCpuUsage();

        m_cpuInfo = new finalcut::FLabel{this};
        m_cpuInfo->setGeometry({3, 4}, {60, 1});

        std::ifstream cpuinfo("/proc/cpuinfo");
        std::string line;
        while (std::getline(cpuinfo, line))
            if (line.starts_with("model name"))
                break;
        m_cpuInfo->setText(line.substr(line.find(':') + 2));

        m_cpuUsageTimer = addTimer(500);
    }

    void onTimer(finalcut::FTimerEvent *e) override
    {
        if (e->getTimerId() == m_cpuUsageTimer)
        {
            parseCpuUsage();
            m_cpuMeter->redraw();
        }
    }

private:
    finalcut::FProgressbar *m_cpuMeter;
    finalcut::FLabel *m_cpuInfo;
    int m_cpuUsageTimer;

    double m_totalCpuLastTick = -1;
    double m_activeCpuLastTick = -1;
};
```

Now we just have to plug in live data from `/proc/stat`. Here's the parser function I ended up building (this was not directly AI-written but it's inspired by an AI-generated prototype function):

``` cpp
void parseCpuUsage()
{
    std::ifstream proc_stat("/proc/stat");
    std::string cpuLine;
    std::getline(proc_stat, cpuLine);
    std::string cpu;
    int user, nice, system, idle, iowait, irq, softirq, steal, guest, guestnice;
    std::stringstream cpuStream(cpuLine);
    cpuStream >> cpu >> user >> nice >> system >> idle >> iowait >> irq >> softirq >> steal >> guest >> guestnice;
    double totalTime = user + nice + system + idle + iowait + irq + softirq + steal + guest + guestnice;
    double activeTime = user + nice + system + irq + softirq + steal + guest + guestnice;
    m_cpuMeter->reset();
    m_cpuMeter->setPercentage(((activeTime - m_activeCpuLastTick) / (totalTime - m_totalCpuLastTick)) * 100);
    m_totalCpuLastTick = totalTime;
    m_activeCpuLastTick = activeTime;
}
```

I won't explain much about this function; you can read [`man 5 proc`](https://www.man7.org/linux/man-pages/man5/proc.5.html) if you want to learn more about how `/proc/stat` formats data. I will point out, however, that I am storing the time values in doubles. Using ints in this case would result in the displayed value clamping to either 0% or 100%. Also of note is the call to `FProgressBar::reset()`; by default, calling `setPercentage()` will add to the value of the progress bar rather than overwrite it.

The final result looks pretty good! Here's a screenshot with [Speedometer](https://browserbench.org/Speedometer2.1) running in the background to provide a load:

{{< figure
    src="https://lorendb.dev/images/final-cut/finaltop-cpu-dialog.png"
    alt="A dialog with a progressbar monitoring CPU usage, along with the processor name"
>}}

Since memory information in Linux is exposed through `/proc/meminfo` in a similar manner to `/proc/cpuinfo`, implementing a memory use widget is not a lot more work than a bit of copy-and-paste. For brevity, I won't show the implementation on this page, but you can find it [here](https://github.com/LorenDB/finaltop/blob/master/main.cpp#L111). Here's how my final implementation looks:

{{< figure
    src="https://lorendb.dev/images/final-cut/finaltop-ram-dialog.png"
    alt="A dialog with a progressbar monitoring memory usage"
    title="If you have swap enabled, this dialog will also show a swap meter."
>}}

## Final thoughts (ha ha)

Apart from the annoying all-caps name, FINAL CUT is very fun and intuitive to work with. I've had a lot of fun working with it so far, and I might keep developing this system monitor out into a fully-fledged system monitor just as an excuse to keep working with FINAL CUT. If you want to play around with the code, I've uploaded it to GitHub as [finaltop](https://github.com/LorenDB/finaltop).

Looking ahead, I have a small list of libraries that I'd like to try out in the future, but if you have any recommendations, please let me know!

{{< chat cpp-spotlight-final-cut-gui >}}
