# go-quartz
A go scheduling library

## About
Inspired by [quartz](https://github.com/quartz-scheduler/quartz) java scheduler.

### Library building blocks
Job interface. Should be implemented by custom jobs for further scheduling
```go
type Job interface {
	Execute()
	Description() string
	Key() int
}
```
Implemented Jobs
- ShellJob
- CurlJob

Scheduler interface
```go
type Scheduler interface {
	//start scheduler
	Start()
	//schedule Job with given Trigger
	ScheduleJob(job Job, trigger Trigger) error
	//get all scheduled Job keys
	GetJobKeys() []int
	//get scheduled Job metadata
	GetScheduledJob(key int) (*ScheduledJob, error)
	//remove Job from execution queue
	DeleteJob(key int) error
	//clear all scheduled jobs
	Clear()
	//shutdown scheduler
	Stop()
}
```
Implemented Schedulers
- StdScheduler

Trigger interface
```go
type Trigger interface {
	NextFireTime(prev int64) (int64, error)
	Description() string
}
```
Implemented Triggers
- CronTrigger
- SimpleTrigger
- RunOnceTrigger

## Examples
```go
sched := quartz.NewStdScheduler()
sched.Start()
cronTrigger, _ := quartz.NewCronTrigger("1/5 * * * * *")
shellJob := quartz.NewShellJob("ls -la")
curlJob, _ := quartz.NewCurlJob(http.MethodGet, "http://worldclockapi.com/api/json/est/now", "", nil)
sched.ScheduleJob(shellJob, cronTrigger)
sched.ScheduleJob(curlJob, quartz.NewSimpleTrigger(time.Second*7))
sched.Stop()
```
More could be found in the examples directory.

## License
Licensed under the MIT License.