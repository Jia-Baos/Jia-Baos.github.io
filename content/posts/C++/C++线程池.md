+++
title = 'C++线程池'
date = '2025-04-20T21:04:32+08:00'
categories = ["C/C++"]
tags = ["C/C++"]
# draft = true
+++

```C++
#include <iostream>           // 标准输入输出流
#include <condition_variable> // 条件变量，用于线程同步
#include <functional>         // 函数对象
#include <mutex>              // 互斥锁
#include <queue>              // 队列
#include <thread>             // 线程
#include <vector>             // 向量容器
#include <sstream>            // 标准字符串流

#define __SOLA_LOGGING_ENABLED

//!
//! convenience macro to log with file and line information
//!
#ifdef __SOLA_LOGGING_ENABLED
#define __SOLA_LOG(level, msg) sola::level(msg, __FILE__, __LINE__);
#else
#define __SOLA_LOG(level, msg)
#endif /* __SOLA_LOGGING_ENABLED */

namespace sola
{
    //!
    //! virtual base class
    //!
    class logger_iface
    {
    public:
        //! ctor
        logger_iface(void) = default;
        //! dtor
        virtual ~logger_iface(void) = default;

        //! copy ctor
        logger_iface(const logger_iface &) = default;
        //! assignment operator
        logger_iface &operator=(const logger_iface &) = default;

    public:
        //!
        //! debug logging
        //!
        //! \param msg message to be logged
        //! \param file file from which the message is coming
        //! \param line line in the file of the message
        //!
        virtual void debug(const std::string &msg, const std::string &file, std::size_t line) = 0;

        //!
        //! info logging
        //!
        //! \param msg message to be logged
        //! \param file file from which the message is coming
        //! \param line line in the file of the message
        //!
        virtual void info(const std::string &msg, const std::string &file, std::size_t line) = 0;

        //!
        //! warn logging
        //!
        //! \param msg message to be logged
        //! \param file file from which the message is coming
        //! \param line line in the file of the message
        //!
        virtual void warn(const std::string &msg, const std::string &file, std::size_t line) = 0;

        //!
        //! error logging
        //!
        //! \param msg message to be logged
        //! \param file file from which the message is coming
        //! \param line line in the file of the message
        //!
        virtual void error(const std::string &msg, const std::string &file, std::size_t line) = 0;
    };

    //!
    //! default logger class provided by the library
    //!
    class logger : public logger_iface
    {
    public:
        //!
        //! log level
        //!
        enum class log_level
        {
            error = 0,
            warn = 1,
            info = 2,
            debug = 3
        };

    public:
        //! ctor
        logger(log_level level = log_level::info);
        //! dtor
        ~logger(void) = default;

        //! copy ctor
        logger(const logger &) = default;
        //! assignment operator
        logger &operator=(const logger &) = default;

    public:
        //!
        //! debug logging
        //!
        //! \param msg message to be logged
        //! \param file file from which the message is coming
        //! \param line line in the file of the message
        //!
        void debug(const std::string &msg, const std::string &file, std::size_t line);

        //!
        //! info logging
        //!
        //! \param msg message to be logged
        //! \param file file from which the message is coming
        //! \param line line in the file of the message
        //!
        void info(const std::string &msg, const std::string &file, std::size_t line);

        //!
        //! warn logging
        //!
        //! \param msg message to be logged
        //! \param file file from which the message is coming
        //! \param line line in the file of the message
        //!
        void warn(const std::string &msg, const std::string &file, std::size_t line);

        //!
        //! error logging
        //!
        //! \param msg message to be logged
        //! \param file file from which the message is coming
        //! \param line line in the file of the message
        //!
        void error(const std::string &msg, const std::string &file, std::size_t line);

    private:
        //!
        //! current log level in use
        //!
        log_level m_level;

        //!
        //! mutex used to serialize logs in multithreaded environment
        //!
        std::mutex m_mutex;
    };

    //!
    //! variable containing the current logger
    //! by default, not set (no logs)
    //!
    extern std::unique_ptr<logger_iface> active_logger;

    std::unique_ptr<logger_iface> active_logger = nullptr;

    static const char black[] = {0x1b, '[', '1', ';', '3', '0', 'm', 0};
    static const char red[] = {0x1b, '[', '1', ';', '3', '1', 'm', 0};
    static const char yellow[] = {0x1b, '[', '1', ';', '3', '3', 'm', 0};
    static const char blue[] = {0x1b, '[', '1', ';', '3', '4', 'm', 0};
    static const char normal[] = {0x1b, '[', '0', ';', '3', '9', 'm', 0};

    logger::logger(log_level level)
        : m_level(level) {}

    void
    logger::debug(const std::string &msg, const std::string &file, std::size_t line)
    {
        if (m_level >= log_level::debug)
        {
            std::lock_guard<std::mutex> lock(m_mutex);
            std::cout << "[" << black << "DEBUG" << normal << "][sola::logger][" << file << ":" << line << "] " << msg << std::endl;
        }
    }

    void
    logger::info(const std::string &msg, const std::string &file, std::size_t line)
    {
        if (m_level >= log_level::info)
        {
            std::lock_guard<std::mutex> lock(m_mutex);
            std::cout << "[" << blue << "INFO " << normal << "][sola::logger][" << file << ":" << line << "] " << msg << std::endl;
        }
    }

    void
    logger::warn(const std::string &msg, const std::string &file, std::size_t line)
    {
        if (m_level >= log_level::warn)
        {
            std::lock_guard<std::mutex> lock(m_mutex);
            std::cout << "[" << yellow << "WARN " << normal << "][sola::logger][" << file << ":" << line << "] " << msg << std::endl;
        }
    }

    void
    logger::error(const std::string &msg, const std::string &file, std::size_t line)
    {
        if (m_level >= log_level::error)
        {
            std::lock_guard<std::mutex> lock(m_mutex);
            std::cerr << "[" << red << "ERROR" << normal << "][sola::logger][" << file << ":" << line << "] " << msg << std::endl;
        }
    }

    //!
    //! debug logging
    //! convenience function used internally to call the logger
    //!
    //! \param msg message to be logged
    //! \param file file from which the message is coming
    //! \param line line in the file of the message
    //!
    void debug(const std::string &msg, const std::string &file, std::size_t line);

    //!
    //! info logging
    //! convenience function used internally to call the logger
    //!
    //! \param msg message to be logged
    //! \param file file from which the message is coming
    //! \param line line in the file of the message
    //!
    void info(const std::string &msg, const std::string &file, std::size_t line);

    //!
    //! warn logging
    //! convenience function used internally to call the logger
    //!
    //! \param msg message to be logged
    //! \param file file from which the message is coming
    //! \param line line in the file of the message
    //!
    void warn(const std::string &msg, const std::string &file, std::size_t line);

    //!
    //! error logging
    //! convenience function used internally to call the logger
    //!
    //! \param msg message to be logged
    //! \param file file from which the message is coming
    //! \param line line in the file of the message
    //!
    void error(const std::string &msg, const std::string &file, std::size_t line);

    void
    debug(const std::string &msg, const std::string &file, std::size_t line)
    {
        if (active_logger)
        {
            active_logger->debug(msg, file, line);
        }
    }

    void
    info(const std::string &msg, const std::string &file, std::size_t line)
    {
        if (active_logger)
        {
            active_logger->info(msg, file, line);
        }
    }

    void
    warn(const std::string &msg, const std::string &file, std::size_t line)
    {
        if (active_logger)
        {
            active_logger->warn(msg, file, line);
        }
    }

    void
    error(const std::string &msg, const std::string &file, std::size_t line)
    {
        if (active_logger)
        {
            active_logger->error(msg, file, line);
        }
    }

    static std::string
    get_tid()
    {
        std::stringstream tmp;
        tmp << std::this_thread::get_id();
        return tmp.str();
    }
};

// 线程池类
class ThreadPool
{
private:
    std::vector<std::thread> pool_;          // 线程池
    std::queue<std::function<void()>> task_; // 任务队列
    std::mutex mutex_;                       // 互斥锁
    std::condition_variable cv_;             // 条件变量
    bool stop_ = false;                      // 停止标志位

public:
    // 构造函数，初始化线程池
    ThreadPool(int num_threads)
    {
        for (size_t i = 0; i < num_threads; ++i)
        {
            AddThread(); // 添加线程
        }
    }

    // 析构函数，销毁线程池
    ~ThreadPool()
    {
        __SOLA_LOG(debug, "~ThreadPool().");
        {
            std::unique_lock<std::mutex> lock(mutex_);
            stop_ = true; // 标志位，停止所有线程
        }

        cv_.notify_all(); // 唤醒所有线程
        __SOLA_LOG(debug, "~ThreadPool() notify_all().");

        for (auto &thread : pool_)
        {
            if (thread.joinable())
            {
                thread.join(); // 等待线程结束
            }
        }
    }

    void AddThread()
    {
        // 添加线程函数
        pool_.emplace_back(
            [this]()
            {
                __SOLA_LOG(debug, "AddThread() tid : " + sola::get_tid() + " start.");
                while (true)
                {
                    std::function<void()> task; // 任务函数
                    {
                        // 获取任务时，需要在条件变量上等待，直到有新任务或线程池被终止
                        std::unique_lock<std::mutex> lock(mutex_);

                        __SOLA_LOG(debug, "AddThread() tid : " + sola::get_tid() + " wait.");

                        // 等待任务队列非空或停止标志位为真
                        cv_.wait(lock, [this]()
                                 { return !task_.empty() || stop_; });

                        if (stop_ && task_.empty())
                        {
                            return; // 停止线程
                        }

                        __SOLA_LOG(debug, "AddThread() tid : " + sola::get_tid() + " wakeup.");

                        task = std::move(task_.front()); // 取出任务
                        task_.pop();                     // 删除任务
                    }
                    task(); // 执行任务
                }
                __SOLA_LOG(debug, "AddThread() tid : " + sola::get_tid() + " exit.");
            });
    }

    // 提交任务
    template <typename F, typename... Args>
    void Commit(F &&f, Args &&...args)
    {
        auto task = std::bind(std::forward<F>(f), std::forward<Args>(args)...);
        {
            // 在添加任务的过程中，需使用互斥量锁住任务队列以实现同步访问
            std::unique_lock<std::mutex> lock(mutex_);
            task_.emplace(task); // 添加任务到队列
        }

        cv_.notify_one(); // 唤醒一个线程执行任务
    }
};

void PrintThread(int num)
{
    // std::mutex mutex_tmp;
    // std::lock_guard<std::mutex> lock(mutex_tmp);
    std::cout << "Thread ID: " << std::this_thread::get_id() << " -> " << num << std::endl;
}

int main(int argc, char *argv[])
{
    sola::active_logger = std::unique_ptr<sola::logger>(new sola::logger(sola::logger::log_level::debug));

    ThreadPool pool(4);
    for (size_t i = 0; i < 8; ++i)
    {
        pool.Commit(PrintThread, i);
    }
    return 0;
}
```