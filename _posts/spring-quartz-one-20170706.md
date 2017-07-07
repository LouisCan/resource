---
title: spring quartz <one>-20170706
date: 2017-07-06 21:10:47
tags: spring
categories: spring
thumbnail: http://osewdhh4j.bkt.clouddn.com/20170706211340.png
blogexcerpt: Quartz是一个完全由Java编写的开源作业调度框架，为在Java应用程序中进行作业调度提供了简单却强大的机制。Quartz允许开发人员根据时间间隔来调度作业。它实现了作业和触发器的多对多的关系，还能把多个作业与不同的触发器关联。简单地创建一个org.quarz.Job接口的Java类，在Job接口实现类里面，添加需要的逻辑到execute()方法中。配置好Job实现类并设定好调度时间表，Quartz就会自动在设定的时间调度作业执行execute()。
---


> Quartz是一个完全由Java编写的开源作业调度框架，为在Java应用程序中进行作业调度提供了简单却强大的机制。Quartz允许开发人员根据时间间隔来调度作业。它实现了作业和触发器的多对多的关系，还能把多个作业与不同的触发器关联。简单地创建一个org.quarz.Job接口的Java类，在Job接口实现类里面，添加需要的逻辑到execute()方法中。配置好Job实现类并设定好调度时间表，Quartz就会自动在设定的时间调度作业执行execute()。

## quartz

**Quartz**是一个完全由java编写的开源作业调度框架。简单地创建一个实现org.quartz.Job接口的java类。
在你的Job接口实现类里面，添加一些逻辑到execute()方法。一旦你配置好Job实现类并设定好调度时间表，Quartz将密切注意剩余时间。当调度程序确定该是通知你的作业的时候，Quartz框架将调用你Job实现类（作业类）上的execute()方法并允许做它该做的事情。无需报告任何东西给调度器或调用任何特定的东西。仅仅执行任务和结束任务即可。如果配置你的作业在随后再次被调用，Quartz框架将在恰当的时间再次调用它。

 1. **Job**：是一个接口，只有一个方法void execute(JobExecutionContext context)，开发者实现该接口定义运行任务，JobExecutionContext类提供了调度上下文的各种信息。Job运行时的信息保存在JobDataMap实例中；
 2. **JobDetail**：Quartz在每次执行Job时，都重新创建一个Job实例，所以它不直接接受一个Job的实例，相反它接收一个Job实现类，以便运行时通过newInstance()的反射机制实例化Job。因此需要通过一个类来描述Job的实现类及其它相关的静态信息，如Job名字、描述、关联监听器等信息，JobDetail承担了这一角色。
 3. **Trigger**：是一个类，描述触发Job执行的时间触发规则。主要有SimpleTrigger和CronTrigger这两个子类。当仅需触发一次或者以固定时间间隔周期执行，SimpleTrigger是最适合的选择；而CronTrigger则可以通过Cron表达式定义出各种复杂时间规则的调度方案：如每早晨9:00执行，周一、周三、周五下午5:00执行等；
 4. **Calendar**：org.quartz.Calendar和java.util.Calendar不同，它是一些日历特定时间点的集合（可以简单地将org.quartz.Calendar看作java.util.Calendar的集合——java.util.Calendar代表一个日历时间点，无特殊说明后面的Calendar即指org.quartz.Calendar）。一个Trigger可以和多个Calendar关联，以便排除或包含某些时间点。假设，我们安排每周星期一早上10:00执行任务，但是如果碰到法定的节日，任务则不执行，这时就需要在Trigger触发机制的基础上使用Calendar进行定点排除。
 5. **Scheduler**：代表一个Quartz的独立运行容器，Trigger和JobDetail可以注册到Scheduler中，两者在Scheduler中拥有各自的组及名称，组及名称是Scheduler查找定位容器中某一对象的依据，Trigger的组及名称必须唯一，JobDetail的组和名称也必须唯一（但可以和Trigger的组和名称相同，因为它们是不同类型的）。Scheduler定义了多个接口方法，允许外部通过组及名称访问和控制容器中Trigger和JobDetail。
  Scheduler可以将Trigger绑定到某一JobDetail中，这样当Trigger触发时，对应的Job就被执行。一个Job可以对应多个Trigger，但一个Trigger只能对应一个Job。可以通过SchedulerFactory创建一个Scheduler实例。Scheduler拥有一个SchedulerContext，它类似于ServletContext，保存着Scheduler上下文信息，Job和Trigger都可以访问SchedulerContext内的信息。SchedulerContext内部通过一个Map，以键值对的方式维护这些上下文数据，SchedulerContext为保存和获取数据提供了多个put()和getXxx()的方法。可以通过Scheduler# getContext()获取对应的SchedulerContext实例；
  
 6. **ThreadPool**：Scheduler使用一个线程池作为任务运行的基础设施，任务通过共享线程池中的线程提高运行效率。

### 调度器
Quartz框架的核心是调度器。
调度器负责管理Quartz应用运行时环境。调度器不是靠自己做所有的工作，而是依赖框架内一些非常重要的部件。Quartz不仅仅是线程和线程管理。为确保可伸缩性，Quartz采用了基于多线程的架构。启动时，框架初始化一套worker线程，这套线程被调度器用来执行预定的作业。这就是Quartz怎样能并发运行多个作业的原理。Quartz依赖一套松耦合的线程池管理部件来管理线程环境。

## code

**pom.xml**
```
		<dependency>
			<groupId>org.quartz-scheduler</groupId>
			<artifactId>quartz</artifactId>
			<version>2.2.2</version>
		</dependency>
```

**ScheduleDto**
```
public class ScheduleDto {

	private String jobName;// 定时任务名称

	private String jobGroup; // 定时任务所属组
	
	private String triggerName; //triggerName
	
	private String triggerGroup; //triggerGroup
	
	private Date StartTime; //开始时间
	
	private Date nextFireTime; //下次调度时间
	
	private String triggerState; //调度状态-NONE，NORMAL，PAUSED，COMPLETE，ERROR，BLOCKED-无，正常，已暂停，完成，错误，已阻止
```

 **spring quartz** 

``` java
@Component
public class QuartzJobComponent {
	
	/**
	 * 查询所以的quartz
	 * @return
	 * @throws Exception
	 */
	public List<ScheduleDto> getScheduleList() throws Exception {
		List<ScheduleDto> list = new ArrayList<>();
		Scheduler scheduler = new StdSchedulerFactory().getScheduler();
		scheduler.getTriggerGroupNames();
		for (String groupName : scheduler.getJobGroupNames()) {
			for (JobKey jobKey : scheduler.getJobKeys(GroupMatcher.jobGroupEquals(groupName))) {
				ScheduleDto dto = new ScheduleDto();
				String jobName = jobKey.getName();
				String jobGroup = jobKey.getGroup();
				dto.setJobName(jobName);
				dto.setJobGroup(jobGroup);
				List<Trigger> triggers = (List<Trigger>) scheduler.getTriggersOfJob(jobKey);
				Trigger trigger = triggers.get(0);
				dto.setTriggerName(trigger.getKey().getName());
				dto.setTriggerGroup(trigger.getKey().getGroup());
				dto.setStartTime(trigger.getStartTime());
				dto.setNextFireTime(trigger.getNextFireTime());
				dto.setTriggerState(scheduler.getTriggerState(trigger.getKey()).name());
				list.add(dto);
			}
		}
		return list;
	}
	
	/**
	 * 查询是否存在返回quartz状态
	 * @return
	 * @throws Exception
	 */
	public String getSchedStatus(String name, String group) throws Exception {
		Scheduler scheduler = new StdSchedulerFactory().getScheduler();
		TriggerKey TriggerKey = new TriggerKey("trigger" + name, group);
		String status = scheduler.getTriggerState(TriggerKey).name();
		return status;
	}
	
	/**
	 * 恢复调度器
	 * @param bean
	 * @throws Exception
	 */
	public void starSched(Integer id, String uri) throws Exception {
		Scheduler scheduler = new StdSchedulerFactory().getScheduler();
		JobKey jobKey = JobKey.jobKey("job" + id, uri);    
		scheduler.resumeJob(jobKey);    
	}
	
	/**
	 * 暂停调度器
	 * @param bean
	 * @throws Exception
	 */
	public void pauseSched(Integer id, String uri) throws Exception {
		Scheduler scheduler = new StdSchedulerFactory().getScheduler();
		JobKey jobKey = JobKey.jobKey("job" + id, uri);    
		scheduler.pauseJob(jobKey);  
	}
	
	/**
	 * 删除调度器
	 * @param bean
	 * @throws Exception
	 */
	public void deleteSched(Integer id, String uri) throws Exception {
		Scheduler scheduler = new StdSchedulerFactory().getScheduler();
		JobKey jobKey = JobKey.jobKey("job" + id, uri);    
		scheduler.deleteJob(jobKey);  
	}
	
	/**
	 * 添加调度器
	 * @param bean
	 * @throws Exception
	 */
	public void addQuartzJob(String frequency,Integer id,String uri) throws Exception{
		Scheduler scheduler = new StdSchedulerFactory().getScheduler();
		if (StringUtils.isNotEmpty(frequency)) {
			String frequencyBefore = frequency.substring(0, frequency.length() - 1);
			String frequencyAfter = frequency.substring(frequency.length() - 1);
			Long afterTime = MetricUtils.getABCTime(frequencyAfter) / 1000;
			/** 获取执行频率的（秒） **/
			int second = (int) (Long.valueOf(frequencyBefore) * afterTime);
			/**创建执行的任务**/
			JobDetail jobDetail = JobBuilder.newJob(SimpleJob.class)
					.withIdentity("job" + id, uri)
					.usingJobData("id", id).build();

			/**创建执行任务的触发器**/
			Trigger trigger = TriggerBuilder.newTrigger()
					.withIdentity("trigger" + id, uri).startNow()
					.withSchedule(SimpleScheduleBuilder.simpleSchedule().withIntervalInSeconds(second)
							.repeatForever())
					.build();
			/**调度任务**/
			scheduler.scheduleJob(jobDetail, trigger);
		}

	}

}

```

**SimpleJob**
```
import org.quartz.Job;
import org.quartz.JobDataMap;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;

public class SimpleJob implements Job {

	@Override
	public void execute(JobExecutionContext context) throws JobExecutionException {

		JobDataMap dataMap = context.getJobDetail().getJobDataMap();
		Integer id = dataMap.getInt("id");
		system.out.println(id);
	
	}

}

```





