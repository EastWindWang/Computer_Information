# Computer_Information
Get computer status information
import wx
from wx import adv
import psutil
import datetime

class my_frame(wx.Frame):
    def __init__(self):
        wx.Frame.__init__(self,None,-1,title="计算机状态助手")

        self.Start_action()#设置开机动画
        self.icon=wx.Icon('computer_ico.ico',wx.BITMAP_TYPE_ICO)#设置窗口图标
        self.SetIcon(self.icon)

        self.CreateStatusBar()

        menubar=wx.MenuBar()

        menu_operate=wx.Menu()
        menu_operate_save=menu_operate.Append(wx.NewId(),"保存","保存计算机参数至安装目录")
        self.Bind(wx.EVT_MENU,self.OnSave,menu_operate_save)
        menu_operate.AppendSeparator()
        menu_operate_exit=menu_operate.Append(wx.NewId(),"退出","退出软件")
        self.Bind(wx.EVT_MENU,self.OnExit,menu_operate_exit)

        menu_about=wx.Menu()
        menu_about_version=menu_about.Append(wx.NewId(),"软件信息","计算机状态助手信息")
        self.Bind(wx.EVT_MENU,self.OnVersion,menu_about_version)
        menu_about.AppendSeparator()
        menu_about_contact=menu_about.Append(wx.NewId(),"联系作者","联系开发者")
        self.Bind(wx.EVT_MENU,self.OnContact,menu_about_contact)


        menubar.Append(menu_operate,"操作")
        menubar.Append(menu_about,"关于")
        self.SetMenuBar(menubar)

        self.Get_Data()


    def OnSave(self,event):
        data_file = open("状态参数.txt", 'a')
        data_file.write("\n\n\n\n\n计算机状态助手检测报告\n\n")
        data_file.write("时间："+str(datetime.datetime.now())+"\n\n")
        data_file.write("*********"+self.cpu.GetLabel()+"*********\n\n")
        data_file.write(self.cpu_info_count.GetLabel()+str(self.cpu_logic_nums)+"\n")
        data_file.write(self.cpu_info_count_phy.GetLabel() + str(self.cpu_phy_nums) + "\n")
        data_file.write(self.info_cpu_fre_max.GetLabel() + str(self.cpu_fre_max) + "MHz\n")

        data_file.write("\n\n*********" + self.mem.GetLabel() + "*********\n\n")
        data_file.write(self.info_mem_total.GetLabel() + str(self.mem_total) + "GB\n")
        data_file.write(self.info_mem_ava.GetLabel() + str( self.mem_ava) + "GB\n")
        data_file.write(self.info_mem_percent.GetLabel() + str( self.mem_percent) + "%\n")

        data_file.write("\n\n*********" + self.disk.GetLabel() + "*********\n\n")
        data_file.write(self.info_disk_total.GetLabel() + str(self.disk_total) + "GB\n")
        data_file.write(self.info_disk_ava.GetLabel() + str(self.disk_ava) + "GB\n")
        data_file.write(self.info_disk_percent.GetLabel() + str(self.disk_percent) + "%\n")

        data_file.write("\n\n*********" + self.battery.GetLabel() + "*********\n\n")
        if self.battery_status == True:
            data_file.write(self.info_battery_status.GetLabel() + "正在充电\n")
            data_file.write(self.info_battery_time.GetLabel() +"已连接电源\n")
        else:
            data_file.write(self.info_battery_status.GetLabel() + "未连接电源\n")
            data_file.write(self.info_battery_time.GetLabel() + "%d:%02d:%02d" % (self.hh, self.mm, self.ss)+"\n")
        data_file.write(self.info_battery_percent.GetLabel() + str(self.battery_percent) + "%\n")

        data_file.write("\n\n*********" + self.sys_title.GetLabel() + "*********\n\n")
        data_file.write( self.info_sys_user.GetLabel() + self.sys_user + "\n")
        data_file.write(self.info_sys_login.GetLabel() + self.sys_login_time + "\n")
        data_file.write(self.info_sys_start.GetLabel() + self.sys_start + "\n")

        data_file.close()
        wx.MessageBox("状态参数已保存至安装目录：状态参数.txt","计算机状态助手")

    def Start_action(self):
        bmp = wx.Image("start.jpg").ConvertToBitmap()
        adv.SplashScreen(bmp, adv.SPLASH_CENTER_ON_SCREEN | adv.SPLASH_TIMEOUT, 4000, None, -1)
        wx.Yield()
    def OnExit(self,event):
        self.Close()
    def OnVersion(self,event):
        wx.MessageBox("软件名称：计算机状态助手\n版本号：V1.0\n编译日期：2018-1-31")
    def OnContact(self,event):
        wx.MessageBox("作者邮箱：EastWind_95@163.com")

    def Get_Data(self):
        self.sizer = wx.BoxSizer(wx.VERTICAL)
        # *********************************************************
        self.cpu_sizer = wx.BoxSizer(wx.VERTICAL)

        self.cpu_title_sizer = wx.BoxSizer(wx.HORIZONTAL)
        self.cpu = wx.StaticText(self, -1, "CPU信息")
        self.cpu.SetForegroundColour("Blue")
        self.cpu_title_sizer.Add(self.cpu, 1, wx.ALIGN_CENTER_HORIZONTAL)

        self.cpu_sizer1 = wx.BoxSizer(wx.HORIZONTAL)

        self.cpu_info_count = wx.StaticText(self, -1, "逻辑核数量：")
        self.cpu_logic_nums = psutil.cpu_count()
        self.cpu_logic_nums_text = wx.TextCtrl(self, -1, "")
        self.cpu_logic_nums_text.SetValue(str(self.cpu_logic_nums))

        self.cpu_info_count_phy = wx.StaticText(self, -1, "物理核数量：")
        self.cpu_phy_nums = psutil.cpu_count(logical=False)
        self.cpu_phy_nums_text = wx.TextCtrl(self, -1, "")
        self.cpu_phy_nums_text.SetValue(str(self.cpu_phy_nums))

        self.info_cpu_fre_max = wx.StaticText(self, -1, "最大频率：")
        self.cpu_fre_max = psutil.cpu_freq().max
        self.cpu_fre_max_text = wx.TextCtrl(self, -1, "")
        self.cpu_fre_max_text.SetValue(str(self.cpu_fre_max) + "MHz")

        self.cpu_sizer1.Add(self.cpu_info_count, 1, wx.EXPAND)
        self.cpu_sizer1.Add(self.cpu_logic_nums_text, 1, wx.EXPAND)
        self.cpu_sizer1.Add(self.cpu_info_count_phy, 1, wx.EXPAND)
        self.cpu_sizer1.Add(self.cpu_phy_nums_text, 1, wx.EXPAND)
        self.cpu_sizer1.Add(self.info_cpu_fre_max, 1, wx.EXPAND)
        self.cpu_sizer1.Add(self.cpu_fre_max_text, 1, wx.EXPAND)

        self.cpu_sizer.Add(self.cpu_title_sizer, 1, wx.ALIGN_CENTER_HORIZONTAL)
        self.cpu_sizer.Add(self.cpu_sizer1, 1, wx.EXPAND)
        # *******************************************************************************
        self.mem_sizer = wx.BoxSizer(wx.VERTICAL)

        self.mem_title_sizer = wx.BoxSizer(wx.HORIZONTAL)
        self.mem = wx.StaticText(self, -1, "内存信息")
        self.mem.SetForegroundColour("Red")
        self.mem_title_sizer.Add(self.mem, 1, wx.ALIGN_CENTER_HORIZONTAL)

        self.mem_sizer_1 = wx.BoxSizer(wx.HORIZONTAL)

        self.info_mem_total = wx.StaticText(self, -1, "内存总量：")
        self.mem_total = round(psutil.virtual_memory().total / (1024.0 * 1024.0 * 1024.0), 3)
        self.mem_total_text = wx.TextCtrl(self, -1, "")
        self.mem_total_text.SetValue(str(self.mem_total) + "GB")

        self.info_mem_ava = wx.StaticText(self, -1, "内存剩余：")
        self.mem_ava = round(psutil.virtual_memory().available / (1024.0 * 1024.0 * 1024.0), 3)
        self.mem_ava_text = wx.TextCtrl(self, -1, "")
        self.mem_ava_text.SetValue(str(self.mem_ava) + "GB")

        self.info_mem_percent = wx.StaticText(self, -1, "内存使用率：")
        self.mem_percent = round(psutil.virtual_memory().percent, 3)
        self.mem_percent_text = wx.TextCtrl(self, -1, "")
        self.mem_percent_text.SetValue(str(self.mem_percent) + "%")

        self.mem_sizer_1.Add(self.info_mem_total, 1, wx.EXPAND)
        self.mem_sizer_1.Add(self.mem_total_text, 1, wx.EXPAND)
        self.mem_sizer_1.Add(self.info_mem_ava, 1, wx.EXPAND)
        self.mem_sizer_1.Add(self.mem_ava_text, 1, wx.EXPAND)
        self.mem_sizer_1.Add(self.info_mem_percent, 1, wx.EXPAND)
        self.mem_sizer_1.Add(self.mem_percent_text, 1, wx.EXPAND)

        self.mem_sizer.Add(self.mem_title_sizer, 1, wx.ALIGN_CENTER_HORIZONTAL)
        self.mem_sizer.Add(self.mem_sizer_1, 1, wx.EXPAND)

#******************************************************************************************
        self.disk_sizer=wx.BoxSizer(wx.VERTICAL)

        self.disk_title_sizer=wx.BoxSizer(wx.HORIZONTAL)
        self.disk=wx.StaticText(self,-1,"系统盘信息")
        self.disk.SetForegroundColour('Green')
        self.disk_sizer.Add(self.disk,1,wx.ALIGN_CENTER_HORIZONTAL)

        self.disk_sizer_1=wx.BoxSizer(wx.HORIZONTAL)

        self.info_disk_total = wx.StaticText(self, -1, "总量：")
        self.disk_total = round(psutil.disk_usage('c:').total/ (1024.0 * 1024.0 * 1024.0), 3)
        self.disk_total_text = wx.TextCtrl(self, -1, "")
        self.disk_total_text.SetValue(str(self.disk_total) + "GB")

        self.info_disk_ava = wx.StaticText(self, -1, "可用：")
        self.disk_ava = round(psutil.disk_usage('c:').free / (1024.0 * 1024.0 * 1024.0), 3)
        self.disk_ava_text = wx.TextCtrl(self, -1, "")
        self.disk_ava_text.SetValue(str(self.disk_ava) + "GB")

        self.info_disk_percent = wx.StaticText(self, -1, "使用率：")
        self.disk_percent = round(psutil.disk_usage('c:').percent, 3)
        self.disk_percent_text = wx.TextCtrl(self, -1, "")
        self.disk_percent_text.SetValue(str(self.disk_percent) + "%")

        self.disk_sizer_1.Add(self.info_disk_total, 1, wx.EXPAND)
        self.disk_sizer_1.Add(self.disk_total_text, 1, wx.EXPAND)
        self.disk_sizer_1.Add(self.info_disk_ava, 1, wx.EXPAND)
        self.disk_sizer_1.Add(self.disk_ava_text, 1, wx.EXPAND)
        self.disk_sizer_1.Add(self.info_disk_percent, 1, wx.EXPAND)
        self.disk_sizer_1.Add(self.disk_percent_text, 1, wx.EXPAND)

        self.disk_sizer.Add(self.disk_title_sizer, 1, wx.ALIGN_CENTER_HORIZONTAL)
        self.disk_sizer.Add(self.disk_sizer_1, 1, wx.EXPAND)
#*************************************************************************************

        self.battery_sizer = wx.BoxSizer(wx.VERTICAL)

        self.battery_title_sizer = wx.BoxSizer(wx.HORIZONTAL)
        self.battery = wx.StaticText(self, -1, "电池状态")
        self.battery.SetForegroundColour('Yellow')
        self.battery_title_sizer.Add(self.battery, 1, wx.ALIGN_CENTER_HORIZONTAL)

        self.battery_sizer_1 = wx.BoxSizer(wx.HORIZONTAL)

        self.info_battery_status = wx.StaticText(self, -1, "充电状态：")
        self.battery_status =psutil.sensors_battery().power_plugged
        self.battery_status_text = wx.TextCtrl(self, -1, "")
        if self.battery_status==True:
            self.battery_status_text.SetValue("正在充电")
        else:
            self.battery_status_text.SetValue("未连接电源")


        self.info_battery_time = wx.StaticText(self, -1, "可用时间：")
        self.battery_time_text = wx.TextCtrl(self, -1, "")
        if self.battery_status==True:
            self.battery_time_text.SetValue("已连接电源")
        else:
            self.battery_time = psutil.sensors_battery()  # 获取电池信息
            self.mm, self.ss = divmod(self.battery_time.secsleft, 60)
            self.hh, self.mm = divmod(self.mm, 60)
            self.battery_time_text.SetValue("%d:%02d:%02d"%(self.hh,self.mm,self.ss))

        self.info_battery_percent = wx.StaticText(self, -1, "电量：")
        self.battery_percent =psutil.sensors_battery().percent
        self.battery_percent_text = wx.TextCtrl(self, -1, "")
        self.battery_percent_text.SetValue(str(self.battery_percent) + "%")

        self.battery_sizer_1.Add(self.info_battery_status, 1, wx.EXPAND)
        self.battery_sizer_1.Add(self.battery_status_text, 1, wx.EXPAND)
        self.battery_sizer_1.Add(self.info_battery_time, 1, wx.EXPAND)
        self.battery_sizer_1.Add(self.battery_time_text, 1, wx.EXPAND)
        self.battery_sizer_1.Add(self.info_battery_percent, 1, wx.EXPAND)
        self.battery_sizer_1.Add(self.battery_percent_text, 1, wx.EXPAND)

        self.battery_sizer.Add(self.battery_title_sizer, 1, wx.ALIGN_CENTER_HORIZONTAL)
        self.battery_sizer.Add(self.battery_sizer_1, 1, wx.EXPAND)
        #*********************************************************************************

        self.sys_sizer=wx.BoxSizer(wx.VERTICAL)

        self.sys_title_sizer=wx.BoxSizer(wx.HORIZONTAL)
        self.sys_title=wx.StaticText(self,-1,"系统信息")
        self.sys_title.SetForegroundColour("Orange")
        self.sys_title_sizer.Add(self.sys_title,1,wx.ALIGN_CENTER_HORIZONTAL)

        self.sys_sizer_1=wx.BoxSizer(wx.HORIZONTAL)

        self.info_sys_user=wx.StaticText(self,-1,"当前用户：")
        self.sys_user=psutil.users()[0][0]
        self.sys_user_text=wx.TextCtrl(self,-1,"")
        self.sys_user_text.SetValue(self.sys_user)

        self.info_sys_login = wx.StaticText(self, -1, "用户登陆时间：")
        self.sys_login_time=datetime.datetime.fromtimestamp(psutil.users()[0][3]).strftime("%Y-%m-%d %H:%M")
        self.sys_login_text = wx.TextCtrl(self, -1, "")
        self.sys_login_text.SetValue(self.sys_login_time)

        self.info_sys_start=wx.StaticText(self,-1,"系统启动时间：")
        self.sys_start=datetime.datetime.fromtimestamp(psutil.boot_time()).strftime("%Y-%m-%d %H:%M")
        self.sys_start_text=wx.TextCtrl(self,-1,"")
        self.sys_start_text.SetValue(self.sys_start)

        self.sys_sizer_1.Add(self.info_sys_user,1,wx.EXPAND)
        self.sys_sizer_1.Add(self.sys_user_text, 1, wx.EXPAND)
        self.sys_sizer_1.Add(self.info_sys_login, 1, wx.EXPAND)
        self.sys_sizer_1.Add(self.sys_login_text, 1, wx.EXPAND)
        self.sys_sizer_1.Add(self.info_sys_start, 1, wx.EXPAND)
        self.sys_sizer_1.Add(self.sys_start_text, 1, wx.EXPAND)

        self.sys_sizer.Add(self.sys_title_sizer,1,wx.ALIGN_CENTER_HORIZONTAL)
        self.sys_sizer.Add(self.sys_sizer_1,1,wx.EXPAND)
        #***************************************************

        self.sizer.Add(self.cpu_sizer, 1, wx.EXPAND)
        self.sizer.Add(self.mem_sizer, 1, wx.EXPAND)
        self.sizer.Add(self.disk_sizer,1,wx.EXPAND)
        self.sizer.Add(self.battery_sizer,1,wx.EXPAND)
        self.sizer.Add(self.sys_sizer,1,wx.EXPAND)
        self.SetSizer(self.sizer)
        self.SetAutoLayout(1)
        self.sizer.Fit(self)



if __name__=="__main__":
    app=wx.App()
    frame=my_frame()
    frame.Show()
    app.MainLoop()
