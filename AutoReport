# -*-coding: UTF-8 -*-
# @Time : 2020/9/11 15:11
# @Auther : wank1r
# @File : daka2.py
# @Software : PyCharm

import os
import smtplib
import time
from email.mime.text import MIMEText
from random import *
import requests
import urllib3
from selenium import webdriver

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
day = time.strftime("%m.%d")
ticks = time.strftime("%H:%M:%S")


class Wxitdaka():
    def __init__(self, username, password, temperature):
        self.username = username
        self.password = password
        self.temperature = temperature
    def daka(self):
        url = 'http://eip.wxit.edu.cn/EIP/user/index.htm'  # 信息门户的登陆页面
        driver = webdriver.Chrome()
        # chromeOptions = webdriver.ChromeOptions()
        # chromeOptions.add_argument('--proxy-server=http://175.21.217.145:10500')
        # driver = webdriver.Chrome(chrome_options=chromeOptions)
        driver.get(url)  # 让自动化模块控制的Chrome浏览器跳转到信息门户登陆页面

        # driver.find_element_by_xpath('//*[@id="north"]/div[2]/div/a[1]').click()

        driver.find_element_by_xpath('//*[@id="username"]').send_keys(
            self.username)  # 输入用户名
        driver.find_element_by_xpath('//*[@id="password"]').send_keys(
            self.password)  # 输入密码
        time.sleep(3)
        driver.find_element_by_xpath(
            '//*[@id="casLoginForm"]/div[2]/button').click()  # 点击登录

        form = 'http://eip.wxit.edu.cn/EIP/cooperative/openCooperative.htm?flowId=529c10546f9e3d8701702346f9603ea7&taskTypeId=1f291e37a4e740dbab930eb2372907d9&_t=684888&_winid=w3592'
        driver.get(form)

        try:  # 判断是否打过了
            iframe = driver.find_element_by_xpath(
                "/html/body/div[5]/div/div[2]/div[2]/iframe")
            driver.switch_to.frame(iframe)
            driver.find_element_by_xpath(
                '//*[@id="messageId"]')
        except Exception as e:
            pass
        else:
            self.email(
                '学号：{} 打卡异常，每天只能填报一次'.format(self.username))
            driver.quit()
            os._exit(0)

        iframe = driver.find_element_by_xpath(
            "/html/body/div[2]/div[2]/table/tbody/tr/td[2]/div[2]/div/iframe")
        driver.switch_to.frame(iframe)
        time.sleep(5)
        driver.find_element_by_xpath('//*[@id="mini-2$ck$0"]').click()  # 我承诺

        driver.switch_to.default_content()  # 跳出iframe
        time.sleep(5)

        driver.find_element_by_css_selector("#mini-17").click()  # 弹窗确定

        time.sleep(5)
        iframe = driver.find_element_by_xpath(
            "/html/body/div[2]/div[2]/table/tbody/tr/td[2]/div[2]/div[2]/iframe")
        driver.switch_to.frame(iframe)

        driver.find_element_by_id("TW$text").send_keys(
            str(self.temperature))  # 填写体温

        driver.switch_to.default_content()  # 跳出iframe

        driver.find_element_by_xpath('//*[@id="sendBtn"]').click()
        time.sleep(1.5)
        driver.find_element_by_xpath('//*[@id="mini-19"]').click()
        print(self.username, '完成打卡！')
        driver.quit()  # 关闭浏览器

    def email(self, r):  # 邮箱提醒
        mail_host = 'smtp.163.com'
        mail_user = '发送的邮箱'
        mail_pass = '授权码'
        sender = '发送的邮箱'
        receivers = ['接收的邮箱']

        content = '{}'.format(r)
        text = '{}--健康打卡结果'.format(day)
        message = MIMEText(content, 'plain', 'utf-8')
        message['Subject'] = text
        message['From'] = sender
        message['To'] = receivers[0]

        try:
            smtpObj = smtplib.SMTP_SSL(mail_host)
            smtpObj.connect(mail_host, 465)
            smtpObj.login(mail_user, mail_pass)
            smtpObj.sendmail(
                sender, receivers, message.as_string())
            smtpObj.quit()
            print('邮箱提醒成功')
        except smtplib.SMTPException as e:
            print('error', e)

    def bark(self, r):  # bark提醒
        url = 'https://api.day.app/App store下载bark/{}--健康打卡结果/{}?isArchive=1'.format(
            day, r)
        requests.get(url=url, verify=False)
        print('brak提醒成功')

    def run(self):
        try:
            try:
                self.daka()
            except Exception as e:
                if Notifications == 1:
                    self.email(
                        '学号：{} 打卡异常，正在重试。错误：{}'.format(self.username, e))
                if Notifications == 2:
                    self.bark('学号：{} 打卡异常，正在重试。错误：{}'.format(self.username, e))
                else:
                    self.email(
                        '学号：{} 打卡异常，正在重试。错误：{}'.format(self.username, e))
                    self.bark('学号：{} 打卡异常，正在重试。错误：{}'.format(self.username, e))
                    self.daka()
        except Exception as e:
            if Notifications == 1:
                self.email('学号：{} 打卡异常，请手动打卡。错误：{}'.format(self.username, e))
                os._exit(0)
            if Notifications == 2:
                self.bark('学号：{} 打卡异常，请手动打卡。错误：{}'.format(self.username, e))
                os._exit(0)
            else:
                self.email('学号：{} 打卡异常，请手动打卡。错误：{}'.format(self.username, e))
                self.bark('学号：{} 打卡异常，请手动打卡。错误：{}'.format(self.username, e))
                os._exit(0)
        if Notifications == 1:
            self.email('学号：{}打卡完成\n体温：{}\n{}'.format(username, temperature,ticks))
        elif Notifications == 2:
            self.bark('学号：{}打卡完成\n体温：{}\n{}'.format(username, temperature,ticks))
        else:
            self.email('学号：{}打卡完成\n体温：{}\n{}'.format(username, temperature,ticks))
            self.bark('学号：{}打卡完成\n体温：{}\n{}'.format(username, temperature,ticks))


if __name__ == '__main__':
    A = 36
    B = 37  # 小数的范围A ~ B
    a = uniform(A, B)
    C = 1  # 随机数的精度round(数值，精度)
    temperature = round(a, C)
    print('体温:', temperature)
    Notifications = 3  # 1为邮箱提醒  2为bark提醒  其他值为双重提醒！
    username = '用户名'
    password = '密码'
    w = Wxitdaka(username, password, temperature)
    w.run()
