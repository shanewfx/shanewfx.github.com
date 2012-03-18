---
layout: post
title: "A Simple Log Tool"
date: 2012-03-18 20:10
comments: true
categories: 
- C++
- Log
---

对于程序员来说，debug是再正常不过的事。

在遇到问题时，通过分析程序的产生的Log文件来查找和验证问题发生的原因。

下面是一个简单的log tool，可以满足一般的log需求。

代码已经放到Github上了，需要的可到[这里](https://github.com/shanewfx/Log)下载。

<!--more-->

该log tool实现了log分级，也支持自己指定log file的存放位置，缺省情况下，log文件为`C:\DebugInfo.log`。

因为log没有加锁，所以在多线程下产生的log会有错乱的现象，因此，在多线程中，可以在Log()函数的开始处加锁来解决。

---

###log.h

{% codeblock %}
#ifndef _LOG_H
#define _LOG_H

#include <string>

#define TEST
#define LOG_FILE_PATH "C:\\DebugInfo.log"

#ifdef TEST
	#define LogData Logger::Instance().Log
	#define SetLogFile Logger::SetLogFilePath
#else
	#define LogData
	#define SetLogFile
#endif

typedef enum tagLogLevel
{
	LOG_TRACE,
	LOG_INFO,
	LOG_WARNING,
	LOG_ERROR,
	LOG_FATAL,
	LOG_NONE = 10,
}LogLevel;

class Logger
{
public:
	static Logger& Instance();

	static void SetLogFilePath(const std::string& strFilePath, bool bAppend = false);
	static void SetLogLevel(const LogLevel Level);
	static void Initialise(bool bAppend = false);
	static void Dispose();

	void Log(const LogLevel Level, const char *Format, ...);

private:
	Logger();
	Logger(Logger const&);
	Logger& operator=(Logger const&);
	~Logger();

	static FILE*        m_hLogFile;
	static std::string	m_strFilePath;
	static LogLevel	    m_LogLevel;
};

#endif//_LOG_H
{% endcodeblock %}


---


###log.cpp

{% codeblock %}
#include "log.h"
#include <stdarg.h>
#include <windows.h>

FILE* Logger::m_hLogFile = NULL;
LogLevel Logger::m_LogLevel = LOG_TRACE;
std::string Logger::m_strFilePath = LOG_FILE_PATH;

const char* LogLevelStr[] = {
    "TRACE",
    "INFO",
    "WARNING",
    "ERROR",
    "CRITICAL",
    "FATAL",
};

Logger& Logger::Instance() 
{
  static Logger LoggerInstance;
  return LoggerInstance;
}

void Logger::SetLogFilePath(const std::string& strFilePath, bool bAppend)
{
	m_strFilePath = strFilePath;
	Dispose();
	Initialise(bAppend);
}

void Logger::SetLogLevel(const LogLevel Level)
{
	m_LogLevel = Level;
}


Logger::Logger()
{
	Initialise(false);
}

Logger::~Logger()
{
	Dispose();
}

void Logger::Initialise(bool bAppend)
{
	if (m_strFilePath.length() > 0) {
		m_hLogFile = bAppend ? fopen(m_strFilePath.c_str(), "a+") 
			             : fopen(m_strFilePath.c_str(), "w+");	
	}
}

void Logger::Dispose()
{
	if (NULL != m_hLogFile) {
		fflush(m_hLogFile);
		fclose(m_hLogFile);
		m_hLogFile = NULL;
	}
}

void Logger::Log(const LogLevel Level, const char *Format, ...)
{
	if (m_LogLevel > Level) return;

	if (NULL == m_hLogFile) return;	

	char szBuffer[1024];

	va_list args;
  va_start(args, Format);
	vsprintf(szBuffer, Format, args);
	va_end(args);

	SYSTEMTIME st;		
	GetLocalTime(&st);
	if (0 > fprintf(m_hLogFile, "[%02u:%02u:%02u:%03u]\t[%s]\t%s\n", 
		st.wHour, st.wMinute, st.wSecond, st.wMilliseconds, 
		LogLevelStr[Level], szBuffer)) {
		Dispose();
	}
	else {
		fflush(m_hLogFile);
	}
}
{% endcodeblock %}

---

###log的使用示例

{% codeblock %}
#include "Log.h"

LogData(LOG_TRACE, "log information\n");

int i = 100;
LogData(LOG_TRACE, "i = %d\n", i);

int j = 10;
LogData(LOG_TRACE, "i = %d, j = %d\n", i, j);

SetLogFile("c:\\log.txt"); //set log file path
{% endcodeblock %}

