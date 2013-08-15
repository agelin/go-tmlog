

[ Golang��־��: go-tmlog ʹ��˵���ļ� ]

@author: heiyeluren
@date: 2013/8/3


1. �����ܹ�:
   a. ���� ��־����(�ͻ���) -> ��־���(��������) �ļܹ�����־�������������ã���־�����һ������Э���ڹ���,
   b. �ܹ���֤��־��֤ʱ���ԣ����ұ�֤�ͻ��˿�����������д����־�����õ���������Ӱ������.
   c. Ӧ�ó���: �ʺϸ�������־��ӡ�ĳ��ϣ����ղ��ԣ��ܹ���ÿ��1�������ĺ�˷����Ͻ�����־��ӡ�������������̫��Ӱ��

2. ��Ҫ����:
   a. ��־����: ���Դ�ӡ5������(notice/trace/debug/warning/fatal)����־�����Ҵ���������������ͣ���������������, ͬʱ���������ﶨ����Щ��־������Ҫ��¼, һ���Ƽ��������ټ�¼ notice/warning/fatal ������־
   b. ��־�ļ�: ��ͬ��־���Ϳ��Ե��������ָ����־�ļ���, һ�㽨�� notice/trace/debug ��һ����־�ļ�, warning/fatal ��һ����־�ļ�
   c. ��־�и�: ֧�ְ��� ��/Сʱ/10���� ���������Զ�������־�ļ��и���������־�ļ���С
   d. ��־ˢ��: ����ָ����־ˢ�̵�ʱ��, ȱʡ1�룬���鲻����3��; �����ǰ��־�ﵽ����90%ռ�ã����Զ�ˢ�̣���֤��������д��־����
   e. ����ģʽ: ֧�ֵ���ģʽ�����������������ն����һЩ��Ϣ��������


3. ����˵��
	#--------------
	#��־��������
	#--------------

	#��־�ļ�λ��, �������ò�ͬ����־��Ϣ���͵���ͬ����־�ļ� (����/var/log/heiyeluren.log)
	log_notice_file_path	= log/heiyeluren.log
	log_debug_file_path	    = log/heiyeluren.log
	log_trace_file_path	    = log/heiyeluren.log
	log_fatal_file_path	    = log/heiyeluren.log.wf
	log_warning_file_path	= log/heiyeluren.log.wf

	#��־�ļ��и����ڣ�1��:day; 1Сʱ:hour; 10����:ten��
	log_cron_time = day

	#��־chan���е�buffer���ȣ��ڸ߲����������£����鲻Ҫ����10240, Խ��Խ��
	#���������1024000�������: 67021478 (�������ֵ���޷�����)
	log_chan_buff_size = 1024000

	#��־ˢ�̵ļ��ʱ�䣬��λ:���룬����500~5000����(0.5s-5s)�����鲻����30��
	log_flush_timer = 1000

	#�Ƿ�����־�����ģʽ(�����ն˴�ӡһЩ��־, 1:����, 0:�ر�)
	log_debug_open = 0

	#�����־�ļ��� (fatal:1,warngin:2,notice:4,trace:8,debug:16)
	#����������Ҫ��ȷ����Ҫ��ӡʲô�������־������������һ����Ҫ��ӡ��־�������ֵ�"��"��������(�����Ϊ��)
	#���ֻ��ӡ���󾯸��notice��־����7�������Ҫ��ӡ������־����31�����ֻ���ӡ��trace�������־������23
	#�����������κ���־����������Ϊ0���ر������ܲ��Ե�ʱ������ˢ��־������Ӱ��
	log_level = 31
 

4. ����ʾ�� (������Բο�Դ���е�main.go�ĵ���)
	================= ������־����Э�̴��� =================
    import "heiyeluren/tmlog"

    // ���ݸ���־���������
    // ˵��: ��Щ����ʵ�ʿ��Է��õ������ļ��������������ֻ����ʾ��ֱ�Ӿ�����map��
    logConf := map[string]string{
        "log_notice_file_path":  "log/heiyeluren.log",
        "log_debug_file_path":   "log/heiyeluren.log",
        "log_trace_file_path":   "log/heiyeluren.log",
        "log_fatal_file_path":   "log/heiyeluren.log.wf",
        "log_warning_file_path": "log/heiyeluren.log.wf",
        "log_cron_time":         "day",
        "log_chan_buff_size":    "1024",
        "log_flush_timer":       "1000",
        "log_debug_open":        "1",
        "log_level":             "31",
    }
    // ���� tmlog ����Э��, �������Ϊtmlog�ķ�������
    tmlog.Log_Run(logConf)


	================= ��ӡ��־�������ʾ�� =================
    //��main��Э�����ӡ��־, �������Ϊtmlog�Ŀͻ�������������������־
    //˵��: ��Щ��������ڵ������κη� tmlog Э��֮�⹤��, ����mainЭ��, ������ĳЩҵ����Э��    

    //��ӡ��־1: ��tmlog����logid, ����log�������, ��ӡnotice��warning������־
    logHandle1 := tmlog.NewLogger("")
    logHandle1.Notice("[logger=logHandle1 msg='The notice message is test']")
    logHandle1.Warning("[logger=logHandle1 msg='The warning message is test']")

    //��ӡ��־2: ���÷�ָ��logid, ��������log�������, ��ӡnotice��warning������־
    //ע��: ����ֻ����ʾ��Ϊ��׷�����ⷽ��, һ�����������һ������ʹ�ö��logid
    logHandle2 := tmlog.NewLogger("123456789")
    logHandle2.Notice("[logger=logHandle2 msg='The notice message is test']")
    logHandle2.Warning("[logger=logHandle2 msg='The warning message is test']")

    //��ӡ��־3: ��һ������Э�����ӡ��־
    go LogTest()

	//go���ú���
    func LogTest() {
        logHandle3 := tmlog.NewLogger("987654321")

        logHandle3.Notice("[logger=logHandle3 msg='The notice message is test']")
        logHandle3.Warning("[logger=logHandle3 msg='The warning message is test']")
    }    

5. ���в��Դ���
	�������ʹ�ð������Բο�Դ���е� main.go �ļ���ͬʱ����ֱ��make���иô���
	���ش��룬Ȼ��ִ�����½ű����в��Դ��룺
	Windows: cd ����Ŀ¼ && make && bin\test.exe
    Unix: cd ����Ŀ¼ && make && bin/test
   
	Ȼ��鿴������ log Ŀ¼���Ƿ������� .log �� .log.wf ����־�ļ������Է������� test����Ȼ��ʹ�� tail ����±������۲���־�ļ��仯��
	
	ע�⣺���ϲ������뱣֤��װ��go 1.0���ϵı������Ļ���֮�ϣ�ͬʱ�ܹ���������go���������²����������С�������˽����ʹ��golang���빤�ߣ���Ԥ��ѧϰһ�¡�

6. ����˵��
	����: heiyeluren
    ����: http://heiyeluren.googlecode.com
	����: http://blog.csdn.net/heiyeshuwu
	΢��: http://weibo.com/heiyeluren
	΢��: heiyeluren2012


