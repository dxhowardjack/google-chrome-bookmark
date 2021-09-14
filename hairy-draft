#import sys
# -*- coding: UTF-8 -*-
import os, sys
import wx, wx.html
import re
import gc

import datetime

from ctypes import *    #改用windll叫user32
#import win32gui
#w=win32gui
currentWindowHandle = 0
#print '>>>>>>' + w.GetWindowText (w.GetForegroundWindow())
#print '>>>>>>' + str(w.GetForegroundWindow())
#print '>>>>>>' + str(w.GetActiveWindow())
#print '>>>>>>' + str(w.GetActiveWindow() == w.GetForegroundWindow())   #(focus window?)
#import json
import time    #time.sleep(0.5) == 0.5 sec
                #wx.Sleep(0.5)   == 0   sec
                
#import win32con

import thread

import webbrowser

#目前 只需要複製、貼上跟寫檔，剪下就是複製+刪除   (都完成了!)

##[做hotkey的難處]：RegisterHotKey雖然有辦法判別到當前視窗，但是會block key，所以會變成只有一個視窗可以用hotkey
##                  GetAsyncKeyState搭配一個thread雖然好用，但是不知道為什麼，卻判別不到當前視窗:(

class MyFileDropTarget(wx.FileDropTarget):
    def __init__(self, window):
        wx.FileDropTarget.__init__(self)
        self.tree = window

    def OnDropFiles(self, x, y, filenames):
        #self.tree.AppendItem(self.tree.RootItem, 'HEY drop!', 2)
    
        pass
        for file in filenames:
            #print ("%s\n" % file)  ##又一個會讓純視窗功能不正常的print
            
            # text_file = open(file, "r+")    #"a+" is good, too! (it creates file.)
                                            # #"w+" purges existing file.
            # pass
            # pass
            # pass
            
            # pass
            # pass
            # #print 'read:' + text_file.read()   #read all
            
            # #text_file.write("line\n")
            # #lines = ["Line 1\n",
            # #         "line 2\n",
            # #         "line 3\n"]
            # #text_file.writelines(lines)
            # #text_file.close()
            
            # #dump(text_file)
            # #print text_file
            
            # pass
            # #print 'seek2', 'asdddfa'.index('g')
            # pass
            # pass
            # pass
            # pass
            
            ###############################
            # regex = re.compile(
            # r"""^.*"
            # (?P<name>.*)
            # ".*$""", re.VERBOSE)
            
            # text_file = open(file, "r")
            # for line in text_file:
                # #print line
                # match = regex.match(line)
                # if match:
                    # pass
                
            # text_file.close()
            ###############################
            
            #regex = re.compile(r'''^ *"(?P<name>[^"]*)"''', re.LOCALE)
            regex = re.compile(r'''^ *"(?P<name>[^"]*)" *: *"(?P<data>.*)" *,? *$''', re.LOCALE)
            regexFolder = re.compile(r'''^ *"children".{3,10}$''', re.LOCALE)
            
            text_file = open(file, "r")
            lines = text_file.readlines()
            
            lineNum = 0
            skipLinesCount = 0
            currentAppendParentNode = self.tree.RootItem
            for line in lines:
                #print line
                if skipLinesCount > 0:
                    skipLinesCount -= 1
                    lineNum += 1
                    continue
                else:
                    match = regexFolder.search(line)
                    if match:
                        currentAppendParentNode = self.tree.AppendItem(currentAppendParentNode, 'New Folder Node'+str(lineNum), 0)
                        lineNum += 1
                    else:
                        match = regex.search(line)
                        if match:
                            #print match.group("name") + '<data>' + match.group("data")
                            #print unicode(match.group("name"), 'utf-8')
                            #print unicode(match.group("name") + '<data>' + match.group("data"), 'utf-8')
                            
                            #if match.group("name") == 'name':
                            #    pass
                            #    #self.tree.AppendItem(self.tree.RootItem, unicode(match.group("data"), 'utf-8'), 2)
                            #    self.tree.SetPyData(self.tree.AppendItem(self.tree.RootItem, unicode(match.group("data"), 'utf-8'), 2), match.group("name"))
                            
                            if match.group("name") == 'type' and match.group("data") == 'folder':   ###>>>>>>>>"type": "folder"
                                ##~~~ 跳過"sync_transaction_version" ~~~##################[start]
                                for k in range(0,3):
                                    match = regex.search(lines[lineNum-1-k])
                                    #if match:
                                    if match and match.group("name") == 'name':
                                        self.tree.SetItemText(currentAppendParentNode, unicode(match.group("data"), 'utf-8'))
                                        #print unicode(match.group("data"), 'utf-8')
                                        break
                                ####~~~~~~~~~跳過"sync_transaction_version"~~~~############[end]
                                    
                                ##############-find date data-########[Folder]
                                list_data = []
                                #for i in range(3,10):
                                for i in range(3,20):   #上限加大，避開"meta_info"
                                    match = regex.search(lines[lineNum - i])
                                    if match and match.group("name") == 'date_modified':
                                        #print 'aaaaaaaaassh'
                                        list_data.append(match.group("data"))
                                        match = regex.search(lines[lineNum - i - 1])
                                        if match and match.group("name") == 'date_added':
                                            list_data.insert(0, match.group("data"))
                                            #print 'aaaaaaaaassh2'
                                            list_data.append(0)    #list_data[2]:type 0 = folder
                                        break
                                self.tree.SetPyData(currentAppendParentNode, list_data)
                                ###########------------------------*
                                    
                                currentAppendParentNode = self.tree.GetItemParent(currentAppendParentNode)
                            else:
                                if match.group("name") == 'name' and (lines[lineNum+1].find('"type": "url"') > -1 or lines[lineNum+2].find('"type": "url"') > -1):  #find找不到是傳-1 ==     #多 跳過"sync_transaction_version"
                                    #print 'iwant+' + match.group("data")
                                    
                                    #pres_name = match.group("name")
                                    pres_data = unicode(match.group("data"), 'utf-8')
                                    ##print unicode(match.group("name") + '<data>' + match.group("data"), 'utf-8')
                                    ##print pres_data   #print unicode怪問題. 但pres_data下面可以照樣用
                                    ##print match.group("name") + '<data>' + match.group("data")
                                    #self.tree.AppendItem(self.tree.RootItem, unicode(match.group("data"), 'utf-8'), 2)
                                    #self.tree.SetPyData(self.tree.AppendItem(self.tree.RootItem, unicode(match.group("data"), 'utf-8'), 2), match.group("name"))
                                    
                                    ##~~~ 跳過"sync_transaction_version" ~~~##################[start]
                                    SKIPsync_transaction_version = 0
                                    if lines[lineNum+2].find('"type": "url"') > -1:
                                        SKIPsync_transaction_version = 1
                                    
                                #    if lines[lineNum+1+k].find(': "url"'):                    ###>>>>>>>>"type": "url"
                                    #print lines[lineNum+2]
                                    #self.tree.SetPyData(self.tree.AppendItem(self.tree.RootItem, pres_data, 2), match.group("data"))
                                    
                                    ##############-find date data-########
                                    list_data = []
                                    #for i in range(2,10):
                                    for i in range(2,20):   #上限加大，避開"meta_info"
                                        match = regex.search(lines[lineNum - i+SKIPsync_transaction_version])
                                        if match and match.group("name") == 'date_added':
                                            #print 'aaaaaaaaassh*'
                                            list_data.append(match.group("data"))
                                            
                                            break
                                    ###########------------------------*
                                    
                                    match = regex.search(lines[lineNum+2+SKIPsync_transaction_version])  #"url": "http://..."
                                    if match:
                                        list_data.append(match.group("data"))
                                        list_data.append(2)     #list_data[2]:type 2 = web page link
                                        
                                        self.tree.SetPyData(self.tree.AppendItem(currentAppendParentNode, pres_data, 2), list_data)
                                        skipLinesCount = 2 + SKIPsync_transaction_version
                                        
                                    ####~~~~~~~~~跳過"sync_transaction_version"~~~~############[end]
                                        
                        lineNum += 1
                
            text_file.close()
            
            ##############################################
            # regex = re.compile(
            # r""".*"         # :)) comment1
            # (?P<name>.*)    # :p  comment2
            # ".*""", re.VERBOSE | re.DOTALL)     #flag可以混用！
            #------------------------------------
            # regex = re.compile(r""".*"(?P<name>.*)".*""", re.DOTALL)   #re.DOTALL  <name>可跨行
            #------------------------------------
            # regex = re.compile(r""".*ass(?P<name>.*)ass.*""", re.DOTALL)
            #------------------------------------
            # regex = re.compile(
            # r""".*ass       # :)) comment1
            # (?P<name>.*)    # :p  comment2
            # ass.*""", re.VERBOSE)
            #------------------------------------
            #re.IGNORECASE
            #re.MULTILINE   #啟用'^'beginning of line   '$'end of line
            #re.DEBUG
            #re.LOCALE
            #re.UNICODE
            
            # lines = 'lin1\nli"neassdataass2\nasd"sda'
            # pass
            
            # #match = regex.match(lines)
            # match = regex.search(lines)   #用search才是真正的逐字元搜尋。
            # if match:
                # pass
            ##############################################
##############################################
#result = []
#regex = re.compile(
#    r"""^-*\s+
#    (?P<name>.*?)\s+
#    \((?P<email>.*?)\)\s+
#    (?:changed\s+status\s+from\s+(?P<previous>.*?)\s+to|became)\s+
#    (?P<new>.*?)\s+@\s+
#    (?P<date>\S+)\s+
#    (?P<time>\S+)\s+
#    -*$""", re.VERBOSE)
#with open("inputfile") as f:
#    for line in f:
#        match = regex.match(line)
#        if match:
#            result.append([
#                match.group("name"),
#                match.group("email"),
#                match.group("previous")
#                # etc.
#            ])
#        else:
#            # Match attempt failed
##############################################
            
            break
            
        self.tree.Expand(self.tree.RootItem)
    
    
class TestTree(wx.TreeCtrl):
    #def __init__(self, panel, style):
    def __init__(self, *args, **kwargs):
        # pass
        #super(TestTree, self).__init__(panel, style)
        super(TestTree, self).__init__(*args, **kwargs)
        
        self.Setting = 0 #<pin-up folders>
        self.SettingR = 0 #<reverse sort>
        
    def OnCompareItems(self, item1, item2):
        """ Overrides OnCompareItems in wx.TreeCtrl. Used by the SortChildren method. """

        if self.Setting == 1:
            if self.GetItemPyData(item1)[2] == 0 and self.GetItemPyData(item2)[2] == 2:
                return -1
            elif self.GetItemPyData(item1)[2] == 2 and self.GetItemPyData(item2)[2] == 0:
                return 1
        
        # Get the item data(add date)
        data1 = int(self.GetItemPyData(item1)[0])
        data2 = int(self.GetItemPyData(item2)[0])

        # Compare the item data(add date)
        if self.SettingR == 1:
            if data1 < data2:
                return 1
            elif data1 > data2:
                return -1
            else:
                return 0
        else:
            if data1 < data2:
                return -1
            elif data1 > data2:
                return 1
            else:
                return 0
    
    def changeSetting(self, setting):
        self.Setting = setting
        
    def changeSettingR(self, setting):
        self.SettingR = setting
    
    
# class SubclassDialog(wx.Dialog):
    # def __init__(self):
        # wx.Dialog.__init__(self, None, -1, 'Dialog Subclass', size=(300, 100))
        # okButton = wx.Button(self, wx.ID_OK, "OK", pos=(15, 15))
        # okButton.SetDefault()
        # cancelButton = wx.Button(self, wx.ID_CANCEL, "Cancel",pos=(115, 15))
class SubclassDialog(wx.Frame):
    def __init__(self, father, tree, textin):
        #wx.Frame.__init__(self, None, size=(300,400), title='ChildFrame')
        titleStr = textin
        if father.cb2.GetValue():
            titleStr = 'url:' + titleStr
        if father.cb1.GetValue():
            titleStr = 'ic:' + titleStr
        wx.Frame.__init__(self, None, size=(300,400), title=titleStr)
        pan = wx.Panel(self)
        # self.txt = wx.TextCtrl(pan, -1, pos=(0,0), size=(100,20), style=wx.DEFAULT)
        # self.but = wx.Button(pan,-1, pos=(10,30), label='Tell parent')
        # self.txt.write(textin)
        
        #self.btn1 = wx.Button(pan, label='Copy')
        #self.btn2 = wx.Button(pan, label='Cut')
        #self.btn3 = wx.Button(pan, label='Enter')
        
        self.tree1 = wx.TreeCtrl(pan, style=wx.TR_DEFAULT_STYLE | wx.TR_MULTIPLE)
        
        self.popupmenu = wx.Menu()
        pm_copy = self.popupmenu.Append(-1, "Copy")
        pm_cut = self.popupmenu.Append(-1, "Cut")
        pm_enter = self.popupmenu.Append(-1, "Enter")
        self.Bind(wx.EVT_MENU, self.OnPopupCopy, pm_copy)
        self.Bind(wx.EVT_MENU, self.OnPopupCut, pm_cut)
        self.Bind(wx.EVT_MENU, self.OnPopupEnter, pm_enter)
        self.tree1.Bind(wx.EVT_RIGHT_DOWN, self.OnShowPopup)
        self.Bind(wx.EVT_TREE_SEL_CHANGED, self.OnSel1, self.tree1)
        self.Selected1 = 0
        self.ItemsSelected1 = []
        
        sizer = wx.GridBagSizer(0, 0)
        #pos的x,y相反.     GridBagSizer(10, 10)指的其實是另一種物件間距，x,y也是相反.
        sizer.Add(self.tree1, pos=(0, 0), span=(1, 3), flag=wx.EXPAND|wx.TOP|wx.LEFT|wx.RIGHT, border=4)
        #sizer.Add(self.btn1, pos=(1, 0), span=(1, 1), flag=wx.EXPAND|wx.ALL, border=4)
        #sizer.Add(self.btn2, pos=(1, 1), span=(1, 1), flag=wx.EXPAND|wx.ALL, border=4)
        #sizer.Add(self.btn3, pos=(1, 2), span=(1, 1), flag=wx.EXPAND|wx.ALL, border=4)
        sizer.AddGrowableRow(0)
        sizer.AddGrowableCol(0)
        pan.SetSizer(sizer)
        self.Bind(wx.EVT_TREE_ITEM_ACTIVATED, self.OnClickF, self.tree1)
        #self.Bind(wx.EVT_TREE_SEL_CHANGED, self.OnClickF2, self.tree1)
        list = []
        self.tree1.AppendItem(self.tree1.RootItem, 'Search')
        
        self.Father = father
        
        
        #if father.Selected and father.tree.GetPyData(father.SelectedItem)[2] == 0:          #if SelectedItem is a folder.
        if father.SelectedFolder:
            pass
            self.RollingBackSearch(father.SelectedItem, textin, list)   #只往該folder裡面找，這是好功能！
            self.selected_item = father.SelectedItem
        else:
            pass
            self.RollingBackSearch(tree.RootItem, textin, list)
            self.selected_item = tree.RootItem
            
        
        self.tree1.Expand(self.tree1.RootItem)
        
        
    def OnSel1(self, event):
        self.Selected1 = event.GetItem()
        self.ItemsSelected1 = self.tree1.GetSelections()
        
    def OnPopupCopy(self, event):
        pass
        del self.Father.SelectedItemS[:]    #clear full list.
        for _item in self.ItemsSelected1:
            list_item = self.tree1.GetPyData(_item)
            self.Father.SelectedItemS.append(list_item[len(list_item)-1])
        self.Father.FCopy(0)
                
    def OnPopupCut(self, event):
        pass
        del self.Father.SelectedItemS[:]    #clear full list.
        for _item in self.ItemsSelected1:
            list_item = self.tree1.GetPyData(_item)
            self.Father.SelectedItemS.append(list_item[len(list_item)-1])
        self.Father.FCopy(0)
        self.Father.OnDeleteChildren(0)
        
    def OnPopupEnter(self, event):
        pass
        #list_item = self.tree1.GetPyData(self.Selected1)
        #print self.Father.tree.GetItemText(list_item[len(list_item)-1])
        
        for _item in self.ItemsSelected1:
            list_item = self.tree1.GetPyData(_item)
            dat_item_data = self.Father.tree.GetPyData(list_item[len(list_item)-1])
            if dat_item_data[2] == 2:
                webbrowser.open(dat_item_data[1])
        
    def OnShowPopup(self, event):
        pass
        pos = event.GetPosition()
        #pos = self.panel.ScreenToClient(pos)
        #self.panel.PopupMenu(self.popupmenu, pos)
        self.tree1.PopupMenu(self.popupmenu, pos)
        
    def RollingBackSearch(self, S_item, textin, _list):
        #copy list
        mlist = []
        for _i in _list:
            mlist.append(_i)

        mlist.append(S_item)
        
        item, cookie = self.tree1.GetFirstChild(S_item)
        index = 0
        case_sensitive = True
        if self.Father.cb1.GetValue():
            case_sensitive = False
        #or_search = False
        #if self.Father.cb2.GetValue():
        #    or_search = True
        url_search = False
        if self.Father.cb2.GetValue():
            url_search = True
        
        last_find_and = False
        sym_find1 = textin.find(":")
        sym_find2 = textin.find(";")
        if sym_find1 > -1 and sym_find2 > -1:
            if sym_find1 < sym_find2:
                last_find_and = True
        if sym_find1 > -1 and sym_find2 == -1:
            last_find_and = True
        
        and_string = list()
        or_string = list()
        comp_string = list()
        gen_list = list(textin)
        str_len = 0
        for _s in gen_list:
            str_len += 1
            if _s != ":" and _s != ";":
                comp_string.append(_s)
            if (_s == ":" or _s == ";") and len(comp_string) > 0:
                sub_string = "".join(comp_string)
                comp_string = []
                #print sub_string
                if last_find_and:
                    and_string.append(sub_string)
                else:
                    or_string.append(sub_string)
                if _s == ":":
                    last_find_and = True
                if _s == ";":
                    last_find_and = False
            if str_len == len(gen_list):
                #print _s
                if last_find_and:
                    sub_string = "".join(comp_string)
                    and_string.append(sub_string)
                else:
                    sub_string = "".join(comp_string)
                    or_string.append(sub_string)
        
        #for _s in and_string:
        #    print "and_" + _s
        #for _s in or_string:
        #    print "or_" + _s
            
        while item:
            index += 1
        
            #if or_search:
            #    boolNotFound = True
            #    if case_sensitive:
            #        for _s in textin.split(':'):
            #            if self.tree1.GetItemText(item).find(_s) >= 0:   #如果它在':'切分當中有一個沒有找到..
            #                boolNotFound = False
            #    else:
            #        for _s in textin.lower().split(':'):
            #            if self.tree1.GetItemText(item).lower().find(_s) >= 0:   #如果它在':'切分當中有一個沒有找到..
            #                boolNotFound = False
            #else:
            #    boolNotFound = False
            #    if case_sensitive:
            #        for _s in textin.split(':'):
            #            if self.tree1.GetItemText(item).find(_s) < 0:   #如果它在':'切分當中有一個沒有找到..
            #                boolNotFound = True
            #    else:
            #        for _s in textin.lower().split(':'):
            #            if self.tree1.GetItemText(item).lower().find(_s) < 0:   #如果它在':'切分當中有一個沒有找到..
            #                boolNotFound = True
            
            #boolNotFound = False
            boolNotFound = True
            and_add = 0
            if not url_search:
                if case_sensitive:
                    for _s in and_string:
                        if self.tree1.GetItemText(item).find(_s) > -1:   #如果它在':'切分當中有一個沒有找到..
                            and_add += 1
                    if and_add == len(and_string) and len(and_string) > 0:
                        boolNotFound = False
                    
                    for _s in or_string:
                        if self.tree1.GetItemText(item).find(_s) > -1:   #如果它在':'切分當中有一個沒有找到..
                            boolNotFound = False
                
                else:
                    for _s in and_string:
                        if self.tree1.GetItemText(item).lower().find(_s.lower()) > -1:   #如果它在':'切分當中有一個沒有找到..
                            and_add += 1
                    if and_add == len(and_string) and len(and_string) > 0:
                        boolNotFound = False
                    
                    for _s in or_string:
                        if self.tree1.GetItemText(item).lower().find(_s.lower()) > -1:   #如果它在':'切分當中有一個沒有找到..
                            boolNotFound = False
                
                # if self.tree1.GetItemText(item).find(textin) > -1:
            else:
                if self.tree1.GetPyData(item)[2] == 2:    #link
                    if case_sensitive:
                        for _s in and_string:
                            if self.tree1.GetPyData(item)[1].find(_s) > -1:   #如果它在':'切分當中有一個沒有找到..
                                and_add += 1
                        if and_add == len(and_string) and len(and_string) > 0:
                            boolNotFound = False
                        
                        for _s in or_string:
                            if self.tree1.GetPyData(item)[1].find(_s) > -1:   #如果它在':'切分當中有一個沒有找到..
                                boolNotFound = False
                    
                    else:
                        for _s in and_string:
                            if self.tree1.GetPyData(item)[1].lower().find(_s.lower()) > -1:   #如果它在':'切分當中有一個沒有找到..
                                and_add += 1
                        if and_add == len(and_string) and len(and_string) > 0:
                            boolNotFound = False
                        
                        for _s in or_string:
                            if self.tree1.GetPyData(item)[1].lower().find(_s.lower()) > -1:   #如果它在':'切分當中有一個沒有找到..
                                boolNotFound = False
                    
                    # if self.tree1.GetItemText(item).find(textin) > -1:
                elif self.tree1.GetPyData(item)[2] == 0:    #folder
                    boolNotFound = True
            
            
            
            if boolNotFound == False:
                #list
                
                #copy list
                mlist2 = []
                for _i in mlist:
                    mlist2.append(_i)
                    
                mlist2.append(item)
                
                if self.tree1.GetPyData(item)[2] == 0:     #folder
                    self.tree1.SetPyData(self.tree1.AppendItem(self.tree1.RootItem, self.tree1.GetItemText(item)), mlist2)
                elif self.tree1.GetPyData(item)[2] == 2:   #link
                    self.tree1.SetPyData(self.tree1.AppendItem(self.tree1.RootItem, '<'+str(index)+'>'+self.tree1.GetItemText(item)), mlist2)
                
                #mlist2.pop() ###?
                
            if self.tree1.ItemHasChildren(item):
                self.RollingBackSearch(item, textin, mlist)
                
            item, cookie = self.tree1.GetNextChild(S_item, cookie)
            
        mlist.pop()
         
    #def OnClickF2(self, event): #workaround.
    #    self.Father.Bind(wx.EVT_TREE_SEL_CHANGED, self.Father.OnSel2, self.Father.tree)
        
    def OnClickF(self, event):
        #self.Father.collapseAll()
        self.Father.tree.CollapseAll()                      #reset expand
        self.Father.tree.UnselectAll() ##SemiBug-printed.   #reset select
        #self.Father.Refresh()
        
        
        if self.selected_item != self.Father.tree.RootItem: #回應SubclassDialog的__init__裡面的選擇
            self.Father.tree.Expand(self.Father.tree.RootItem)
            preExpandList = []
            TmpParent = self.Father.tree.GetItemParent(self.selected_item)
            while TmpParent != self.Father.tree.RootItem:
                #print self.Father.tree.GetItemText(TmpParent)
                preExpandList.insert(0,TmpParent)
                TmpParent = self.Father.tree.GetItemParent(TmpParent)
            
            for i in range(0,len(preExpandList)):
                pass
                self.Father.tree.Expand(preExpandList[i])

        
        ctrlint = 0
        
        item = event.GetItem()
        list = self.tree1.GetPyData(item)
        for _i in list:
        
            pass
            pass
            
            self.Father.tree.Expand(_i)                     #expand target
            
            if ctrlint == len(list)-1:
                # self.Father.sByPass = 2  #
                #self.Father.Bind(wx.EVT_TREE_SEL_CHANGED, self.Father.OnSel2, self.Father.tree)
                self.Father.tree.SetFocusedItem(_i)
                self.Father.tree.ToggleItemSelection(_i)    #select target  #奇怪只有.py有效，.pyw無效。改redirect會有幫助，記得先用rex replaceAll清掉print。
                #self.Father.SelectedItem = _i
                #time.sleep(1.5)
                #self.Father.ToggleItem(_i)
            ctrlint += 1
        pass
        
        # self.Father.tree.Update()
        # self.Father.tree.Refresh()
        # self.Father.tree.Update()
        # self.Father.tree.Refresh()
        # self.Father.panel.Update()
        # self.Father.panel.Refresh()
        # self.Father.panel.Update()
        # self.Father.panel.Refresh()
        # self.Father.Update()
        # self.Father.Refresh()
        
        #self.Father.Update()
        #self.Father.Refresh()   ####!_!
        ##fails...
        
        # slist = 'asd:dsa'.split(':')
        # for _s in slist:
            # pass
            # pass
        
aboutText = """<p>This program was made by <b>Li Mu-Hua</b>. Locate at Taiwan.<br>
contact me: <a href="mailto:pc_50313@hotmail.com">pc_50313@hotmail.com</a></p>""" 
#http://wiki.wxpython.org
class HtmlWindow(wx.html.HtmlWindow):
    def __init__(self, parent, id, size=(600,400)):
        wx.html.HtmlWindow.__init__(self,parent, id, size=size)
        if "gtk2" in wx.PlatformInfo:
            self.SetStandardFonts()

    def OnLinkClicked(self, link):
        wx.LaunchDefaultBrowser(link.GetHref())
        
class AboutBox(wx.Dialog):
    def __init__(self):
        wx.Dialog.__init__(self, None, -1, "About <<project>>",
            style=wx.DEFAULT_DIALOG_STYLE|wx.THICK_FRAME|wx.RESIZE_BORDER|
                wx.TAB_TRAVERSAL)
        hwin = HtmlWindow(self, -1, size=(400,200))
        #vers = {}
        #vers["python"] = sys.version.split()[0]
        #vers["wxpy"] = wx.VERSION_STRING
        #hwin.SetPage(aboutText % vers)
        hwin.SetPage(aboutText)
        btn = hwin.FindWindowById(wx.ID_OK)
        irep = hwin.GetInternalRepresentation()
        hwin.SetSize((irep.GetWidth()+25, irep.GetHeight()+10))
        self.SetClientSize(hwin.GetSize())
        self.CentreOnParent(wx.BOTH)
        self.SetFocus()
        
class TestFrame(wx.Frame):
    def __init__(self):
        wx.Frame.__init__(self, None, title="tree: misc tests", size=(450,500))

        il = wx.ImageList(16,16)

        self.fldridx = il.Add(wx.ArtProvider.GetBitmap(wx.ART_FOLDER, wx.ART_OTHER, (16,16)))
        self.fldropenidx = il.Add(wx.ArtProvider.GetBitmap(wx.ART_FILE_OPEN,   wx.ART_OTHER, (16,16)))
        self.fileidx = il.Add(wx.ArtProvider.GetBitmap(wx.ART_NORMAL_FILE, wx.ART_OTHER, (16,16)))
        
        
        self.panel = wx.Panel(self)
        
        #self.tree = wx.TreeCtrl(self.panel, style=wx.TR_DEFAULT_STYLE | wx.TR_EDIT_LABELS | wx.TR_MULTIPLE)
        self.tree = TestTree(self.panel, style=wx.TR_DEFAULT_STYLE | wx.TR_EDIT_LABELS | wx.TR_MULTIPLE)
        
        
        ########
        self.dt = MyFileDropTarget(self.tree)
        self.tree.SetDropTarget(self.dt)
        ########
        
        self.tc = wx.TextCtrl(self.panel)
        self.btn = wx.Button(self.panel, label='WriteURL')
        self.btn2 = wx.Button(self.panel, label=';S;e;a:r:c:h')
        self.cb1 = wx.CheckBox(self.panel)
        self.cb2 = wx.CheckBox(self.panel)
        
        self.cb1.Bind(wx.EVT_MOTION, self.onMouseOver1)
        self.cb2.Bind(wx.EVT_MOTION, self.onMouseOver2)
        
        sizer = wx.GridBagSizer(2, 2)
        sizer.Add(self.tree, pos=(0, 0), span=(1, 8), flag=wx.EXPAND|wx.TOP|wx.LEFT|wx.RIGHT, border=4)
        #sizer.Add(self.tree, pos=(0, 0), span=(1, 7), flag=wx.EXPAND|wx.TOP|wx.LEFT|wx.RIGHT, border=4)
        #sizer.Add(text, pos=(1, 0), span=(10, 10), flag=wx.EXPAND|wx.TOP|wx.BOTTOM|wx.LEFT|wx.RIGHT, border=5)
        sizer.Add(self.tc, pos=(1, 0), span=(1, 4), flag=wx.EXPAND|wx.TOP|wx.BOTTOM|wx.LEFT, border=4)
        sizer.Add(self.btn, pos=(1, 4), span=(1, 1), flag=wx.EXPAND|wx.ALL, border=4)
        sizer.Add(self.btn2, pos=(1, 5), span=(1, 1), flag=wx.EXPAND|wx.ALL, border=4)
        sizer.Add(self.cb1, pos=(1, 6), span=(1, 1), flag=wx.EXPAND|wx.ALL, border=0)
        sizer.Add(self.cb2, pos=(1, 7), span=(1, 1), flag=wx.EXPAND|wx.ALL, border=0)
        sizer.AddGrowableRow(0)
        sizer.AddGrowableCol(0)
        
        self.panel.SetSizer(sizer)
        #self.panel.SetSizerAndFit(sizer)    #This method calls SetSizer and then Sizer.SetSizeHints
        
        
        self.tree.AssignImageList(il)

        #root = self.tree.AddRoot("wx.Object")
        root = self.tree.AddRoot("Root")
#        self.tree.SetItemPyData(root, None)
        date_str = str(long(time.time()*1000000 + 11644473600000000)) #11644473600000000 = 1970y-1601y
        list_data = []
        list_data.append(date_str) #date_added
        list_data.append(date_str) #date_modified
        list_data.append(0)
        self.tree.SetItemPyData(root, list_data)    #fix for root copy properly.
        #self.tree.SetItemImage(root, self.fldridx,wx.TreeItemIcon_Normal)
        self.tree.SetItemImage(root, self.fldropenidx,wx.TreeItemIcon_Normal)
        self.tree.SetItemImage(root, self.fldropenidx,wx.TreeItemIcon_Expanded)
        
        #tree = ["A","B","C","D","E","F","G","H"]
        tree = []

        self.AddTreeNodes(root, tree)

        #self.tree.Expand(root)

        menu = wx.Menu()
        self.mi_check6 = menu.AppendCheckItem(-1, '<huge deletion>')
        mi = menu.Append(-1, "Delete           <del>")
        mi_a = menu.Append(-1, "Copy           <ctrl-C>")
        mi_b = menu.Append(-1, "Cut              <ctrl-X>")
        mi_c = menu.Append(-1, "Paste            <ctrl-V>")
        #mi_a = menu.Append(-1, "Copy")
        #mi_b = menu.Append(-1, "Cut")
        #mi_c = menu.Append(-1, "Paste")
        self.mi_check2 = menu.AppendCheckItem(-1, '<paste into>')
        #self.mi_check2.Check(True)    #.IsChecked() .Toggle()
        menu.AppendSeparator()
        #self.mi_check = menu.AppendCheckItem(-1, 'use hotkey', 'hotkey works when window is active')
        self.mi_check = menu.AppendCheckItem(-1, 'share-clip', 'The copy data can be shared between clipboard.')
        self.mi_check.Check(True)    #.IsChecked()
        #self.mi_check.Toggle()
        menu.AppendSeparator()
        menu.AppendSeparator()
        mi_save = menu.Append(-1, "Save as file")
        menu.AppendSeparator()
        menu.AppendSeparator()
        mi_load_B = menu.Append(-1, "Load from browser")
        menu.AppendSeparator()
        mi_save_B = menu.Append(-1, "Save to browser")
        menu.AppendSeparator()
        mi_goto_B = menu.Append(-1, "Goto browser path")
        # mi1 = menu.Append(-1, "AppendItem")
        # mi2 = menu.Append(-1, "InsertItem")
        # mi3 = menu.Append(-1, "InsertItemBefore")
        self.Bind(wx.EVT_MENU, self.OnDeleteChildren, mi)
        self.Bind(wx.EVT_MENU, self.FCopy, mi_a)
        self.Bind(wx.EVT_MENU, self.FCut, mi_b)
        self.Bind(wx.EVT_MENU, self.FPaste, mi_c)
        self.Bind(wx.EVT_MENU, self.Fsave, mi_save)
        self.Bind(wx.EVT_MENU, self.FloadB, mi_load_B)
        self.Bind(wx.EVT_MENU, self.FsaveB, mi_save_B)
        self.Bind(wx.EVT_MENU, self.FgotoB, mi_goto_B)
        # self.Bind(wx.EVT_MENU, self.FAppendItem, mi1)
        # self.Bind(wx.EVT_MENU, self.FInsertItem, mi2)
        # self.Bind(wx.EVT_MENU, self.FInsertItemBefore, mi3)
        #self.Bind(wx.EVT_TREE_ITEM_ACTIVATED, self.OnActivate, self.tree)
        self.Bind(wx.EVT_TREE_ITEM_ACTIVATED, self.OnDClick, self.tree)
        self.Bind(wx.EVT_TREE_SEL_CHANGED, self.OnSel, self.tree)
        #self.Bind(wx.EVT_TREE_SEL_CHANGED, self.OnSel2, self.tree)
        
        self.deltatime = 0
        self.temptime = 0
        self.Bind(wx.EVT_IDLE, self.OnIdle)
        
        
        self.btn.Bind(wx.EVT_BUTTON, self.FSetURL)
        self.btn2.Bind(wx.EVT_BUTTON, self.SearchFunc)
        self.SelectedItem = 0   #for Write button (, or Paste)
        
        self.SelectedItemS = [] #for Delete, Copy
        
        self.MyCopyBuffer = []
        
        
        self.mb = wx.MenuBar()
        self.mb.Append(menu, "Tests")
        self.SetMenuBar(self.mb)
        
        
        
        
        menu_Adv = wx.Menu()
        mi_adv_addFolder = menu_Adv.Append(-1, 'add folder')
        mi_adv_addLink   = menu_Adv.Append(-1, 'add link')
        menu_Adv.AppendSeparator()
        mi_adv_sortDate  = menu_Adv.Append(-1, 'Sort by date')
        self.mi_check5 = menu_Adv.AppendCheckItem(-1, '<reverse sort>')
        self.mi_check3 = menu_Adv.AppendCheckItem(-1, '<pin-up folders>')
        menu_Adv.AppendSeparator()
        menu_Adv.AppendSeparator()
        mi_adv_or  = menu_Adv.Append(-1, 'or   op1<->op2 (())')
        mi_adv_and = menu_Adv.Append(-1, 'and op1<->op2  ()')
        mi_adv_not = menu_Adv.Append(-1, 'not  op1<-  op2')
        #menu_Adv.SetTitle(u'fff')
        self.Bind(wx.EVT_MENU, self.AddFolder, mi_adv_addFolder)
        self.Bind(wx.EVT_MENU, self.AddLink, mi_adv_addLink)
        self.Bind(wx.EVT_MENU, self.SortByDate, mi_adv_sortDate)
        self.Bind(wx.EVT_MENU, self.OrLogic, mi_adv_or)
        self.Bind(wx.EVT_MENU, self.AndLogic, mi_adv_and)
        self.Bind(wx.EVT_MENU, self.NotLogic, mi_adv_not)
        self.mi_check4 = menu_Adv.AppendCheckItem(-1, '<downward>')
        
        
        self.mb.Append(menu_Adv, "Advance")
        #self.SetMenuBar(self.mb)
        
        
        
        
        ####################################找到換Menu名字的辦法
        self.reservedMenuCount = 2
        #menu.SetLabel(2,'fuck fuck')
        #print menu.GetLabelText(2)
        menu_ = wx.Menu()
        self.mb.Append(menu_, "Tests2")
        #menu.SetTitle(u'fuck you')
        #menu2.SetTitle(u'fuck you')
        #print self.mb.GetMenus()
        
        #print self.mb.GetMenu(1)
        menu1 = self.mb.GetMenu(self.reservedMenuCount)
        menu2 = wx.Menu()
        self.mb.Replace(self.reservedMenuCount, menu2, 'date_added')
        #print menu2 == menu_
        #print menu1 == menu_
        del menu1
        
        self.TIME_ZONE_GAP = (time.timezone / 3600) * 3600000000
        
        
        menu_About = wx.Menu()
        m_about = menu_About.Append(wx.ID_ABOUT, "&author", "Information about the author")
        self.Bind(wx.EVT_MENU, self.OnAbout, m_about)
        self.mb.Append(menu_About, "About")
        
#        self.hotKeyId = 100
#        self.RegisterHotKey(
#            self.hotKeyId, #a unique ID for this hotkey
#            #win32con.MOD_ALT|win32con.MOD_SHIFT, #the modifier key
#            0,
#            #win32con.VK_F1) #the key to watch for
#            #ord('D'))
#            46)     #win32con.VK_DELETE = 46    from dump(win32con)
#        self.Bind(wx.EVT_HOTKEY, self.handleHotKey, id=self.hotKeyId)
#        
#    #win32con.MOD_ALT = 1
#    #win32con.MOD_CONTROL = 2
#    #win32con.MOD_SHIFT = 4
#    #win32con.MOD_WIN = 8
#        self.hotKeyId = 101
#        self.RegisterHotKey(
#            self.hotKeyId, #a unique ID for this hotkey
#            #win32con.MOD_CONTROL, #the modifier key
#            2,
#            #win32con.VK_F1) #the key to watch for
#            ord('C'))
#            #46)     #win32con.VK_DELETE = 46    from dump(win32con)
#        self.Bind(wx.EVT_HOTKEY, self.handleHotKey1, id=self.hotKeyId)
#        
#        self.hotKeyId = 102
#        self.RegisterHotKey(
#            self.hotKeyId, #a unique ID for this hotkey
#            #win32con.MOD_CONTROL, #the modifier key
#            2,
#            #win32con.VK_F1) #the key to watch for
#            ord('X'))
#            #46)     #win32con.VK_DELETE = 46    from dump(win32con)
#        self.Bind(wx.EVT_HOTKEY, self.handleHotKey2, id=self.hotKeyId)
#        
#        self.hotKeyId = 103
#        self.c(
#            self.hotKeyId, #a unique ID for this hotkey
#            #win32con.MOD_CONTROL, #the modifier key
#            2,
#            #win32con.VK_F1) #the key to watch for
#            ord('V'))
#            #46)     #win32con.VK_DELETE = 46    from dump(win32con)
#        self.Bind(wx.EVT_HOTKEY, self.handleHotKey3, id=self.hotKeyId)
#        
#    def handleHotKey3(self, evt):
#        pass
#        
#    def handleHotKey2(self, evt):
#        pass
#        
#    def handleHotKey1(self, evt):
#        pass
#        
#    def handleHotKey(self, evt):
#        #'frame.IsActive()' from wx.Frame繼承wx.TopLevelWindow
#        pass
#        #dump(win32con)
#        self.OnDeleteChildren(evt)
        
        
        # # # # thread.start_new_thread(self.loop0, ())
        #thread.start_new_thread(gloop, ())
#        
#        self.operationFlag = False
    #    self.hybridFlag = False     #for FCut()
    
    
        self.popupmenu = wx.Menu()
        for text in "Cut Copy Paste".split():
            item = self.popupmenu.Append(-1, text)
            self.Bind(wx.EVT_MENU, self.OnPopupItemSelected, item)
        #self.panel.Bind(wx.EVT_CONTEXT_MENU, self.OnShowPopup)
        #self.tree.Bind(wx.EVT_CONTEXT_MENU, self.OnShowPopup)
        #self.tree.Bind(wx.EVT_RIGHT_UP, self.OnShowPopup)
        self.tree.Bind(wx.EVT_RIGHT_DOWN, self.OnShowPopup)
        
        self.SaveToB = False
        
        # self.sByPass = 0
        self.sByPassTrigger1 = 1
        self.sByPassTrigger2 = 0
        
    # def collapseAll(self):
        # self.tree.CollapseAll()
        
        
        self.SelectedFolder = False   #workaround for SubclassDialog __init__
        
    def OnAbout(self, event):
        dlg = AboutBox()
        dlg.ShowModal()
        dlg.Destroy()  
        
    def onMouseOver1(self, event):
        event.GetEventObject().SetToolTipString("ignore case")
    def onMouseOver2(self, event):
        event.GetEventObject().SetToolTipString("url search")
        
    def SearchFunc(self, event):
        #wx.MessageBox("SearchBox '%s'" % "SEARCH", "ERR")
        dialog = SubclassDialog(self, self.tree, self.tc.GetLineText(0))
        dialog.Show()
        # result = dialog.ShowModal()
        # if result == wx.ID_OK:
            # pass
        # else:
            # pass
            
        #dialog.Destroy()
        
    def recOrLogic(self, S_item1, S_item2):
        #Or邏輯因為要融合到1,所以採2詢問1的方式looping
        item2, cookie2 = self.tree.GetFirstChild(S_item2)
        while item2:
            if self.tree.ItemHasChildren(item2) or self.tree.GetPyData(item2)[2] == 0:#如果2為資料夾
                coincident = False
                item1, cookie1 = self.tree.GetFirstChild(S_item1)
                while item1:
                    if self.tree.ItemHasChildren(item1):
                        if self.tree.GetItemText(item1) == self.tree.GetItemText(item2):
                            coincident = True
                            break
                    else:
                        if self.tree.GetPyData(item1)[2] == 0:
                            if self.tree.GetItemText(item1) == self.tree.GetItemText(item2):
                                coincident = True
                                break
                    item1, cookie1 = self.tree.GetNextChild(S_item1, cookie1)
                if coincident:
                    self.recOrLogic(item1, item2)
                else:
                    aitem = self.tree.AppendItem(S_item1, self.tree.GetItemText(item2), 0)
                    self.tree.SetPyData(aitem, self.tree.GetPyData(item2))
                    #self.recAdd(aitem, item2)
                    self.recOrLogic(aitem, item2)
            else:                               #如果2為網頁連結
                coincident = False
                item1, cookie1 = self.tree.GetFirstChild(S_item1)
                while item1:
                    if self.tree.ItemHasChildren(item1):
                        pass
                    else:
                        if self.tree.GetItemText(item1) == self.tree.GetItemText(item2) and ((self.tree.GetPyData(item1)[2] == 0 and self.tree.GetPyData(item2)[2] == 0) or self.tree.GetPyData(item1)[1] == self.tree.GetPyData(item2)[1]): #if name and url is the same.
                            coincident = True
                            break
                    item1, cookie1 = self.tree.GetNextChild(S_item1, cookie1)
                if coincident:
                    pass
                else:
                    self.tree.SetPyData(self.tree.AppendItem(S_item1, self.tree.GetItemText(item2), 2), self.tree.GetPyData(item2))
                
            item2, cookie2 = self.tree.GetNextChild(S_item2, cookie2)
        
    def OrLogic(self, event):
        if len(self.SelectedItemS) != 2 or self.tree.GetPyData(self.SelectedItemS[0])[2] != 0 or self.tree.GetPyData(self.SelectedItemS[1])[2] != 0:
            wx.MessageBox("Select two folders!", "Error")
            return
            
        if self.mi_check4.IsChecked():
            S_item2 = self.SelectedItemS[len(self.SelectedItemS)-2]
            S_item1 = self.SelectedItemS[len(self.SelectedItemS)-1]
        else:
            S_item1 = self.SelectedItemS[len(self.SelectedItemS)-2]
            S_item2 = self.SelectedItemS[len(self.SelectedItemS)-1]
        # pass
        # pass
        # pass
        # for item in self.SelectedItemS:
            # pass
        
        self.recOrLogic(S_item1, S_item2)
        
    def recDelete(self, item):
        if self.tree.ItemHasChildren(item):
            _item, _cookie = self.tree.GetFirstChild(item)
            while _item:
                self.recDelete(_item)
                _item, _cookie = self.tree.GetNextChild(item, _cookie)
                
            self.tree.Delete(item)
        else:
            self.tree.Delete(item)
        
    def recAndLogic(self, S_item1, S_item2):
        #And邏輯採1詢問2的方式looping
        #[Bug]:同層次間的子資料夾務必要不同名，不然結果會只以第一個遇到的為參考。
        item1, cookie1 = self.tree.GetFirstChild(S_item1)
        while item1:
            ReadyToDeleteTF = False
            ReadyToDeleteItem = item1
            if self.tree.ItemHasChildren(item1):#如果2為資料夾
                #print '1'
                coincident = False
                item2, cookie2 = self.tree.GetFirstChild(S_item2)
                while item2:
                    if self.tree.ItemHasChildren(item2):
                        if self.tree.GetItemText(item1) == self.tree.GetItemText(item2):
                            coincident = True
                            break
                    else:
                        pass
                    item2, cookie2 = self.tree.GetNextChild(S_item2, cookie2)
                if coincident:
                    self.recAndLogic(item1, item2)
                    if not self.tree.ItemHasChildren(item1):
                        #self.recDelete(item1)
                        ReadyToDeleteTF = True
                else:
                    #self.recDelete(item1)
                    ReadyToDeleteTF = True
                    
            else:                               #如果2為網頁連結
                #print '2'
                coincident = False
                item2, cookie2 = self.tree.GetFirstChild(S_item2)
                while item2:
                    if self.tree.ItemHasChildren(item2):
                        pass
                    else:
                        if self.tree.GetItemText(item1) == self.tree.GetItemText(item2) and ((self.tree.GetPyData(item1)[2] == 0 and self.tree.GetPyData(item2)[2] == 0) or self.tree.GetPyData(item1)[1] == self.tree.GetPyData(item2)[1]): #if name and url is the same.
                            coincident = True
                            break
                    item2, cookie2 = self.tree.GetNextChild(S_item2, cookie2)
                if coincident:
                    pass
                else:
                    #self.recDelete(item1)
                    ReadyToDeleteTF = True
                
            item1, cookie1 = self.tree.GetNextChild(S_item1, cookie1)
            
            if ReadyToDeleteTF:
                self.recDelete(ReadyToDeleteItem)
        
    def AndLogic(self, event):
        if len(self.SelectedItemS) != 2 or self.tree.GetPyData(self.SelectedItemS[0])[2] != 0 or self.tree.GetPyData(self.SelectedItemS[1])[2] != 0:
            wx.MessageBox("Select two folders!", "Error")
            return
            
        if self.mi_check4.IsChecked():
            S_item2 = self.SelectedItemS[len(self.SelectedItemS)-2]
            S_item1 = self.SelectedItemS[len(self.SelectedItemS)-1]
        else:
            S_item1 = self.SelectedItemS[len(self.SelectedItemS)-2]
            S_item2 = self.SelectedItemS[len(self.SelectedItemS)-1]
        
        self.recAndLogic(S_item1, S_item2)
        
    def recNotLogic(self, S_item1, S_item2):
        #And邏輯採1詢問2的方式looping
        #[Bug]:同層次間的子資料夾務必要不同名，不然結果會只以第一個遇到的為參考。
        item1, cookie1 = self.tree.GetFirstChild(S_item1)
        while item1:
            ReadyToDeleteTF = False
            ReadyToDeleteItem = item1
            if self.tree.ItemHasChildren(item1):#如果2為資料夾
                #print '1'
                #print self.tree.GetItemText(item1)
                coincident = False
                item2, cookie2 = self.tree.GetFirstChild(S_item2)
                while item2:
                    if self.tree.ItemHasChildren(item2):
                        if self.tree.GetItemText(item1) == self.tree.GetItemText(item2):
                            coincident = True
                            break
                    else:
                        pass
                    item2, cookie2 = self.tree.GetNextChild(S_item2, cookie2)
                if coincident:
                    self.recNotLogic(item1, item2)
                    if not self.tree.ItemHasChildren(item1):
                        #self.recDelete(item1)
                        ReadyToDeleteTF = True
                else:
                    pass
                    
            else:                               #如果2為網頁連結
                #print '2'
                #print self.tree.GetItemText(item1)
                coincident = False
                item2, cookie2 = self.tree.GetFirstChild(S_item2)
                while item2:
                    if self.tree.ItemHasChildren(item2):
                        pass
                    else:
                        #print 'a:' + self.tree.GetItemText(item1) + ', b:' + self.tree.GetItemText(item2)
                        if self.tree.GetItemText(item1) == self.tree.GetItemText(item2) and ((self.tree.GetPyData(item1)[2] == 0 and self.tree.GetPyData(item2)[2] == 0) or self.tree.GetPyData(item1)[1] == self.tree.GetPyData(item2)[1]): #if name and url is the same.
                            coincident = True
                            #print 'coincident'
                            break
                    item2, cookie2 = self.tree.GetNextChild(S_item2, cookie2)
                if coincident:
                    #self.recDelete(item1)
                    ReadyToDeleteTF = True
                else:
                    pass
                
            item1, cookie1 = self.tree.GetNextChild(S_item1, cookie1)
            
            if ReadyToDeleteTF:
                self.recDelete(ReadyToDeleteItem)
        
    def NotLogic(self, event):
        if len(self.SelectedItemS) != 2 or self.tree.GetPyData(self.SelectedItemS[0])[2] != 0 or self.tree.GetPyData(self.SelectedItemS[1])[2] != 0:
            wx.MessageBox("Select two folders!", "Error")
            return
            
        if self.mi_check4.IsChecked():
            S_item2 = self.SelectedItemS[len(self.SelectedItemS)-2]
            S_item1 = self.SelectedItemS[len(self.SelectedItemS)-1]
        else:
            S_item1 = self.SelectedItemS[len(self.SelectedItemS)-2]
            S_item2 = self.SelectedItemS[len(self.SelectedItemS)-1]
        
        self.recNotLogic(S_item1, S_item2)
        
    def AddFolder(self, event):
        item = self.tree.GetFocusedItem()
        if self.mi_check2.IsChecked() == False and item == self.tree.RootItem:
            return  #如果對一個RootItem做平貼,那就請回吧，省得報錯
        date_str = str(long(time.time()*1000000 + 11644473600000000)) #11644473600000000 = 1970y-1601y
        list_data = []
        list_data.append(date_str) #date_added
        list_data.append(date_str) #date_modified
        list_data.append(0)
        if self.mi_check2.IsChecked() and (item == self.tree.RootItem or self.tree.GetPyData(item)[2] == 0):
            self.tree.SetItemPyData(self.tree.AppendItem(item, 'New AddFolder', 0), list_data)
        else:
            self.tree.SetItemPyData(self.tree.InsertItem(self.tree.GetItemParent(item), item, 'New AddFolder', 0), list_data)
        pass
        
    def AddLink(self, event):
        item = self.tree.GetFocusedItem()
        if self.mi_check2.IsChecked() == False and item == self.tree.RootItem:
            return  #如果對一個RootItem做平貼,
        date_str = str(long(time.time()*1000000 + 11644473600000000)) #11644473600000000 = 1970y-1601y
        list_data = []
        list_data.append(date_str) #date_added
        list_data.append("")
        list_data.append(2)
        if self.mi_check2.IsChecked() and (item == self.tree.RootItem or self.tree.GetPyData(item)[2] == 0):
            self.tree.SetItemPyData(self.tree.AppendItem(item, 'New AddLink', 2), list_data)
        else:
            self.tree.SetItemPyData(self.tree.InsertItem(self.tree.GetItemParent(item), item, 'New AddLink', 2), list_data)
        pass
        
    def SortByDate(self, event):
        itemSelected = self.tree.GetFocusedItem()
        if itemSelected == self.tree.RootItem:
            return
        item = self.tree.GetItemParent(itemSelected)
        #item = self.tree.GetItemParent(self.tree.GetFocusedItem())
        
        if self.mi_check3.IsChecked():
            self.tree.changeSetting(1)
        else:
            self.tree.changeSetting(0)
        
        if self.mi_check5.IsChecked():
            self.tree.changeSettingR(1)
        else:
            self.tree.changeSettingR(0)
        
        self.tree.SortChildren(item)
        #if item != self.tree.RootItem:
        #    self.tree.SortChildren(item)
        #pass
        
        
    def FgotoB(self, event):
        os.system("explorer %localappdata%\Google\Chrome\User Data\Default")
        
    def FsaveB(self, event):
        dlg = wx.MessageDialog(None, "Are you sure?  (you have to restart the browser)",'A Message Box',wx.YES_NO | wx.ICON_QUESTION)
        retCode = dlg.ShowModal()
        if (retCode == wx.ID_YES):
            pass
            #os.system("explorer %localappdata%\Google\Chrome\User Data\Default")
            self.SaveToB = True
            self.Fsave(0)
        else:
            pass
        dlg.Destroy()
        
    def FloadB(self, event):
        #str = os.system("echo %localappdata%")
        #print os.environ['localappdata']
        path = [os.environ['localappdata']+"\Google\Chrome\User Data\Default\Bookmarks"]
        #print path[0]
        self.dt.OnDropFiles(0,0,path)
        
    def recCollectToFile(self, parent, indent, text_file):
        if self.tree.ItemHasChildren(parent):
        #    text_file.write('   ' * indent + '{\n')
            item, cookie = self.tree.GetFirstChild(parent)
            firstIndentSkip = True
            while item:
                if firstIndentSkip:
                    #text_file.write('   ' * indent + '{\n')
                    text_file.write('{\n')
                    firstIndentSkip = False
                else:
                    text_file.write(', {\n')
                    
                data = self.tree.GetPyData(item)
            #    if len(data) == 3:
                if data[2] == 0: #type 0 = folder
                    text_file.write('   ' * (indent+1) + '"children": [ ')
                    self.recCollectToFile(item, (indent+1), text_file)
                    text_file.write(' ],\n')
                    
                    #text_file.write('   ' * (indent+1) + 'folder\n')
                    #text_file.write('   ' * (indent+1) + unicode(self.tree.GetItemText(item), 'utf-8') + '\n')
                    #text_file.write('   ' * (indent+1) + self.tree.GetItemText(item).decode('utf-8') + '\n')
                    #text_file.write('   ' * (indent+1) + self.tree.GetItemText(item).encode('utf-8') + '\n')    #encode轉成sequence byte.

                    text_file.write('   ' * (indent+1) + '"date_added": "' + data[0] + '",\n')
                    text_file.write('   ' * (indent+1) + '"date_modified": "' + data[1] + '",\n')
                    text_file.write('   ' * (indent+1) + '"id": "0",\n')
                    text_file.write('   ' * (indent+1) + '"name": "' + self.tree.GetItemText(item).encode('utf-8') + '",\n')
                    text_file.write('   ' * (indent+1) + '"type": "folder"\n')
                    
                elif data[2] == 2: #type 2 = web link
                    #text_file.write('   ' * (indent+1) + 'web link\n')
                    #text_file.write('   ' * (indent+1) + self.tree.GetItemText(item).encode('utf-8') + '\n')    #encode轉成sequence byte.
                    
                    text_file.write('   ' * (indent+1) + '"date_added": "' + data[0] + '",\n')
                    text_file.write('   ' * (indent+1) + '"id": "0",\n')
                    text_file.write('   ' * (indent+1) + '"name": "' + self.tree.GetItemText(item).encode('utf-8') + '",\n')
                    text_file.write('   ' * (indent+1) + '"type": "url",\n')
                    text_file.write('   ' * (indent+1) + '"url": "' + data[1] + '"\n')
            #    else:
            #        pass
            #        pass
            #        pass
            
                text_file.write('   ' * indent + '}')
                item, cookie = self.tree.GetNextChild(parent, cookie)
        #    text_file.write('   ' * indent + '}\n')
        
    def Fsave(self, event):
        
        if self.SaveToB:
            text_file = open(os.environ['localappdata']+'\Google\Chrome\User Data\Default\Bookmarks', "w")
            self.SaveToB = False
        else:
            #text_file = open('Mzybks', "w")
            # wildcard = "Python source (*.py)|*.py|" \
                    # "Compiled Python (*.pyc)|*.pyc|" \
                    # "All files (*.*)|*.*"
            wildcard = "All files (*.*)|*.*"
            dialog = wx.FileDialog(None, "Choose a file", os.getcwd(), "", wildcard, wx.SAVE)   #wx.OPEN
            if dialog.ShowModal() == wx.ID_OK:
                pass
                text_file = open(dialog.GetPath(), "w")
                dialog.Destroy()
            else:
                dialog.Destroy()
                return
        
        #text_file.write('ffs')
        #print self.MyCopyBuffer
        #text_file.write(str(self.MyCopyBuffer))
        #text_file.write('\n')
        #text_file.writeline('not such a function#')
        
    #    file_head = {'{\n',
    #                 '   "checksum": "dee472a23d192b5727a7a635f1d6b4f1",\n',
    #                 '   "roots": {\n'}
    #    text_file.writelines(file_head)
        text_file.write('{\n')
        text_file.write('   "checksum": "dee472a23d192b5727a7a635f1d6b4f1",\n')
        text_file.write('   "roots": {\n')
        
        if self.tree.ItemHasChildren(self.tree.RootItem):
            
            item, cookie = self.tree.GetFirstChild(self.tree.RootItem)
            while item:
                data = self.tree.GetPyData(item)
                if self.tree.GetItemText(item) == "Bookmarks bar" or self.tree.GetItemText(item) == u"書籤列" or self.tree.GetItemText(item) == u"书签栏" or self.tree.GetItemText(item) == "Desktop bookmarks" or self.tree.GetItemText(item) == u"電腦版書籤" or self.tree.GetItemText(item) == u"桌面书签":
                    pass
                    text_file.write('      "bookmark_bar": {\n')
                    text_file.write('         "children": [ ')
                    self.recCollectToFile(item, 3, text_file)
                    
                    text_file.write(' ],\n')
                    text_file.write('         "date_added": "' + data[0] + '",\n')
                    text_file.write('         "date_modified": "' + data[1] + '",\n')
                    text_file.write('         "id": "1",\n')
                    text_file.write('         "name": "Bookmarks bar",\n')
                    text_file.write('         "type": "folder"\n')
                    text_file.write('      },\n')
                if self.tree.GetItemText(item) == "Other bookmarks" or self.tree.GetItemText(item) == u"其他書籤" or self.tree.GetItemText(item) == u"其他书签":
                    pass
                    text_file.write('      "other": {\n')
                    text_file.write('         "children": [ ')
                    self.recCollectToFile(item, 3, text_file)
                    
                    text_file.write(' ],\n')
                    text_file.write('         "date_added": "' + data[0] + '",\n')
                    text_file.write('         "date_modified": "' + data[1] + '",\n')
                    text_file.write('         "id": "2",\n')
                    text_file.write('         "name": "Other bookmarks",\n')
                    text_file.write('         "type": "folder"\n')
                    text_file.write('      },\n')
                if self.tree.GetItemText(item) == "Mobile bookmarks" or self.tree.GetItemText(item) == u"行動版書籤" or self.tree.GetItemText(item) == u"移动设备书签":
                    pass
                    text_file.write('      "synced": {\n')
                    #text_file.write('         "children": [  ],\n')
                    text_file.write('         "children": [ ')
                    self.recCollectToFile(item, 3, text_file)
                    
                    text_file.write(' ],\n')
                    text_file.write('         "date_added": "' + data[0] + '",\n')
                    text_file.write('         "date_modified": "' + data[1] + '",\n')
                    text_file.write('         "id": "3",\n')
                    text_file.write('         "name": "Mobile bookmarks",\n')
                    text_file.write('         "type": "folder"\n')
                    text_file.write('      }\n')
                    break
                item, cookie = self.tree.GetNextChild(self.tree.RootItem, cookie)
            
            
        #self.recCollectToFile(self.tree.RootItem, 1, text_file)
        
        
        #self.recCollectToFile(self.tree.RootItem, 1, text_file)
        
        
        
        # text_file.write('      "synced": {\n')
        # text_file.write('         "children": [  ],\n')
        # text_file.write('         "date_added": "13040838444822250",\n')
        # text_file.write('         "date_modified": "0",\n')
        # text_file.write('         "id": "3",\n')
        # text_file.write('         "name": "Mobile bookmarks",\n')
        # text_file.write('         "type": "folder"\n')
        # text_file.write('      }\n')
        
        text_file.write('   },\n')
        text_file.write('   "version": 1\n')
        text_file.write('}\n')
    #    file_end = {'   },\n',
    #                '   "version": 1\n',
    #                '}\n'}
    #    text_file.writelines(file_end)
        
        text_file.close()
        
            # #text_file.write("line\n")
            # #lines = ["Line 1\n",
            # #         "line 2\n",
            # #         "line 3\n"]
            # #text_file.writelines(lines)
            # #text_file.close()
        
        
#    def loop0(self):
    def OnIdle(self, event):
#        while 1:
            #[Description("SHIFT key")]
            #VK_SHIFT = 0x10,
            #
            #[Description("CTRL key")]
            #VK_CONTROL = 0x11,
            #
            #[Description("ALT key")]
            #VK_MENU = 0x12,
            #
            #VK_DELETE   0x2E
            #C key   0x43
            #X key   0x58
            #V key   0x56
            ##以上通通不準！
            #
            #要用它自訂的:
            #wx.WXK_DELETE
            #wx.WXK_CONTROL
            #wx.WXK_SHIFT
            #wx.WXK_ALT
            #wx.WXK_F1
            
            #self.SetFocus()
            ##self.Raise()
            #print 'IsActive(): ' + str(self.IsActive()) + str(self.HasFocus()) + str(self.IsShown())
            #print w.GetActiveWindow() == w.GetForegroundWindow()   #竟然沒用，這在thread裡面跑也一直報False。==
            
            #if frame.IsActive() and self.operationFlag == False:
            #if w.GetActiveWindow() == w.GetForegroundWindow() and self.operationFlag == False:
#            if self.operationFlag == False:
            #print currentWindowHandle
            
        control_del = 0
        control_ctrl = 0
        control_c = 0
        control_x = 0
        control_v = 0
            
        #if currentWindowHandle == w.GetForegroundWindow():
        if currentWindowHandle == user32.GetForegroundWindow() and self.FindFocus() == self.tree:
        
            if self.sByPassTrigger1 == 2:
                self.sByPassTrigger1 = 1
                self.sByPassTrigger2 = 0
            if self.sByPassTrigger1 == 1:
                self.sByPassTrigger2 += 1
                if self.sByPassTrigger2 > 1000:
                    self.sByPassTrigger2 = 10
                
            if self.sByPassTrigger2 == 1:
                #self.Bind(wx.EVT_TREE_SEL_CHANGED, self.OnSel, self.tree)
                pass
            
            self.deltatime += time.time() - self.temptime
            self.temptime = time.time()
            if self.deltatime > 0.5:
                
                #clear key buffer pending.
                if user32.GetAsyncKeyState(0x2E):
                    control_del = 1
                else:
                    control_del = 0
                if user32.GetAsyncKeyState(0x11):
                    control_ctrl = 1
                else:
                    control_ctrl = 0
                if user32.GetAsyncKeyState(ord('C')):
                    control_c = 1
                else:
                    control_c = 0
                if user32.GetAsyncKeyState(ord('X')):
                    control_x = 1
                else:
                    control_x = 0
                if user32.GetAsyncKeyState(ord('V')):
                    control_v = 1
                else:
                    control_v = 0
                
                #if wx.GetKeyState(wx.WXK_DELETE):
                #if user32.GetAsyncKeyState(0x2E):   #Delete
                if control_del == 1:
                    #print 'K(Del)'
                    # self.operationFlag = True
                    self.OnDeleteChildren(0)
                    self.deltatime = 0
                    #time.sleep(0.5) #timeout
                #if wx.GetKeyState(wx.WXK_CONTROL) and wx.GetKeyState(ord('C')):
                #if user32.GetAsyncKeyState(0x11) and user32.GetAsyncKeyState(ord('C')):
                if control_ctrl == 1 and control_c == 1:
                    #print 'K(Copy)'
                    # self.operationFlag = True
                    self.FCopy(0)
                    self.deltatime = 0
                    #time.sleep(0.5) #timeout
                #if wx.GetKeyState(wx.WXK_CONTROL) and wx.GetKeyState(ord('x')):
                #if user32.GetAsyncKeyState(0x11) and user32.GetAsyncKeyState(ord('X')):
                if control_ctrl == 1 and control_x == 1:
                    #print 'K(Cut)'
                    # self.operationFlag = True
                    # slef.hybridFlag    = True
                    self.FCut(0)
                    self.deltatime = 0
                    #time.sleep(0.5) #timeout
                #if wx.GetKeyState(wx.WXK_CONTROL) and wx.GetKeyState(ord('V')):
                #if user32.GetAsyncKeyState(0x11) and user32.GetAsyncKeyState(ord('V')):
                if control_ctrl == 1 and control_v == 1:
                    #print 'K(Paste)'
                    # self.operationFlag = True
                    self.FPaste(0)
                    self.deltatime = 0
                    #time.sleep(0.5) #timeout
        else:
            #clear key buffer pending.
            user32.GetAsyncKeyState(0x2E)
            user32.GetAsyncKeyState(0x11)
            user32.GetAsyncKeyState(ord('C'))
            user32.GetAsyncKeyState(ord('X'))
            user32.GetAsyncKeyState(ord('V'))
            #control_del = 0
            #control_ctrl = 0
            #control_c = 0
            #control_x = 0
            #control_v = 0
        
            if self.sByPassTrigger1 == 1:
                self.sByPassTrigger1 = 2
                self.sByPassTrigger2 = 0
            if self.sByPassTrigger1 == 2:
                self.sByPassTrigger2 += 1
                if self.sByPassTrigger2 > 1000:
                    self.sByPassTrigger2 = 10
            
            if self.sByPassTrigger2 == 1:
                #self.Bind(wx.EVT_TREE_SEL_CHANGED, self.OnSel2, self.tree)
                pass
            
        #time.sleep(0.1)        ##會導致ui lag==
            #print 'loop 0 done at:', time.ctime()
        

    def OnShowPopup(self, event):
        pass
        pos = event.GetPosition()
        #pos = self.panel.ScreenToClient(pos)
        #self.panel.PopupMenu(self.popupmenu, pos)
        self.tree.PopupMenu(self.popupmenu, pos)

    def OnPopupItemSelected(self, event):
        item = self.popupmenu.FindItemById(event.GetId())
        text = item.GetText()
        #wx.MessageBox("You selected item '%s'" % text)
        if text == 'Cut':
            self.FCut(0)
        if text == 'Copy':
            self.FCopy(0)
        if text == 'Paste':
            self.FPaste(0)
            
        
    def FCut(self, event):
        self.FCopy(event)
        
        self.OnDeleteChildren(event)
        
    #    self.operationFlag = False
    #    self.hybridFlag    = False
        pass
        
    #雖然是可以function了，可是這種感覺就是寫歪了，相信這些recursive可以再被調整一下:)     #調好了.
    #def Paste_m2(self, item, slist):    #append    #這行會讓pyminifier出錯
    def Paste_m2(self, item, slist):
        if slist[1][2] == 0:   #type = 0 (folder)
            #new_item = self.tree.InsertItem(self.tree.GetItemParent(item), item, slist[0], 0)
            new_item = self.tree.AppendItem(item, slist[0], 0)
            self.tree.SetPyData(new_item, slist[1])         ##有自我的人，也很容易犯錯啊！！
            if len(slist) == 3: #如果存在folder_list這第三樣內容
                for sub_item in slist[2]:
                    self.Paste_m2(new_item, sub_item)   #u need a new append method
        else:
            if slist[1][2] == 2:   #type = 2 (web link)
                #self.tree.SetPyData(self.tree.InsertItem(self.tree.GetItemParent(item), item, slist[0], 2), slist[1][1])
                self.tree.SetPyData(self.tree.AppendItem(item, slist[0], 2), slist[1])
    
#    def Paste_m1(self, item, slist): #slist is a single item    #會寫這個的人，要是沒一點自我，怎麼可能寫得下去。==[+]D.Gray-man OP2 Single - Brightdown.mp3
#        #print len(list)
#        if slist[1][2] == 0:   #type = 0 (folder)
#            new_item = self.tree.InsertItem(self.tree.GetItemParent(item), item, slist[0], 0)
#            self.tree.SetPyData(new_item, slist[2])
#            for sub_item in slist[2]:
#                self.Paste_m2(new_item, sub_item)   #u need a new append method
#        else:
#            if slist[1][2] == 2:   #type = 2 (web link)
#                self.tree.SetPyData(self.tree.InsertItem(self.tree.GetItemParent(item), item, slist[0], 2), slist[1][1])
#        #for listem in self.MyCopyBuffer:
#            #print 'type' + str(listem[1][2])
#            #print unicode(str(listem), 'utf-8')
#            #print isinstance(listem, unicode)   #isinstance(str, unicode) 可用來判斷是否為unicode
#            #print '====================='
#            #if listem[1][2] == 0:   #type = 0 (folder)
#            #    self.tree.SetPyData(self.tree.InsertItem(item, listem, listem[0], 0), listem[2])
#            #    self.Paste_m1(item, listem[2])
#            #else:
#            #    if listem[1][2] == 2:   #type = 2 (web link)
#            #        self.tree.SetPyData(self.tree.InsertItem(item, listem, listem[0], 2), listem[1][1])
#        #time.sleep(0.1)
#        #wx.Sleep(0.1)
#        #pass  
        
    def FPaste(self, event):
        item = self.tree.GetFocusedItem()
        #print '(Paste) InsertItem', self.tree.GetItemText(item)
        #self.tree.SetPyData(self.tree.InsertItem(self.tree.GetItemParent(item), item, "InsertItemName", 2), 'InsertItemData')
        
        if self.mi_check2.IsChecked() == False and item == self.tree.RootItem:
            return  #如果對一個RootItem做平貼,那就請回吧，省得報錯
        
        if self.mi_check.IsChecked() == True:
            success = False
            data = wx.TextDataObject()
            if wx.TheClipboard.Open():
                success = wx.TheClipboard.GetData(data)
                wx.TheClipboard.Close()
            if success:
                #print data.GetText()
                self.MyCopyBuffer = eval(data.GetText())    #eval()跟str()是相對的，eval()很好用！
            else:
                #wx.MessageBox("no data in the clipboard in the required format","Error")
                pass
            
            
        #if self.tree.GetPyData(item)[2] == 0 and self.mi_check2.IsChecked() == True:
        if (self.tree.RootItem == item or self.tree.GetPyData(item)[2] == 0) and self.mi_check2.IsChecked() == True: #fix paste-into root
            self.MyCopyBuffer = self.MyCopyBuffer[::-1] #reverse list. example: list.reverse() or reverse(list)
            
            
        for i in range(len(self.MyCopyBuffer),0,-1):
            if self.MyCopyBuffer[i-1][1][2] == 0:   #type = 0 (folder)
                if self.mi_check2.IsChecked() and (item == self.tree.RootItem or self.tree.GetPyData(item)[2] == 0): #不僅<paste-into>大打勾，還要確認Paste得目標item是不是一個folder type或RootItem
                    #想不到這個new_item放在if: else:裡面竟然還可以用@@
                    new_item = self.tree.AppendItem(item, self.MyCopyBuffer[i-1][0], 0)
                else:
                    new_item = self.tree.InsertItem(self.tree.GetItemParent(item), item, self.MyCopyBuffer[i-1][0], 0)
                self.tree.SetPyData(new_item, self.MyCopyBuffer[i-1][1])    ##有自我的人，也很容易犯錯啊！！
                if len(self.MyCopyBuffer[i-1]) == 3: #如果存在folder_list這第三樣內容
                    for sub_item in self.MyCopyBuffer[i-1][2]:
                        self.Paste_m2(new_item, sub_item)   #u need a new append method
            else:
                if self.MyCopyBuffer[i-1][1][2] == 2:   #type = 2 (web link)
                    if self.mi_check2.IsChecked() and (item == self.tree.RootItem or self.tree.GetPyData(item)[2] == 0): #不僅<paste-into>大打勾，還要確認Paste得目標item是不是一個folder type
                        self.tree.SetPyData(self.tree.AppendItem(item, self.MyCopyBuffer[i-1][0], 2), self.MyCopyBuffer[i-1][1])
                    else:
                        self.tree.SetPyData(self.tree.InsertItem(self.tree.GetItemParent(item), item, self.MyCopyBuffer[i-1][0], 2), self.MyCopyBuffer[i-1][1])
#        for i in range(len(self.MyCopyBuffer),0,-1):
#            self.Paste_m1(item, self.MyCopyBuffer[i-1])
            #print self.MyCopyBuffer[i-1]
        #for listem in self.MyCopyBuffer:
            #self.Paste_m1(item, listem)
            #print 'len'+str(len(self.MyCopyBuffer))+'+i+'+str(i-1)
            
        #print '(Paste) InsertItem', self.tree.GetItemText(item)
        
    #    if self.mi_check.IsChecked() == False:     #log_MyCopyBuffer內容正常，所以這看起來像是TreeCtrl的連續操作自己crash掉了。
    #        pass
    #        pass
    #        pass
            
    #    time.sleep(1)
    #        
    #    self.operationFlag = False
        pass
        
#####################################################
##
#    def PrintAllItems(self, parent, indent=0):
#        text = self.tree.GetItemText(parent)
#        pass
#        indent += 4
#        item, cookie = self.tree.GetFirstChild(parent)
#        while item:
#            if self.tree.ItemHasChildren(item):
#                self.PrintAllItems(item, indent)
#            else:
#                text = self.tree.GetItemText(item)
#                pass
#            item, cookie = self.tree.GetNextChild(parent, cookie)
##
#####################################################
#--------------------------------------------------------
    ###
    ###   folder[] = [
    ###               name,
    ###               PyData[date_added, date_modified, type=0],
    ###               folder_list[folder[], or wblink[]...]
    ###               ]
    ###   wblink[] = [
    ###               name,
    ###               PyData[date_added, url, type=2]
    ###               ]
    ###
     ## ###########################################
      #
       
    def recCollectToList(self, parent):
        rlist = []
        #text = self.tree.GetItemText(parent)
        #print ' ' * indent, text
        #indent += 4
        
        rlist.append(self.tree.GetItemText(parent))
        rlist.append(self.tree.GetPyData(parent))
        
        folder_list = []
        
        item, cookie = self.tree.GetFirstChild(parent)
        while item:
            if self.tree.ItemHasChildren(item):
                folder_list.append( self.recCollectToList(item) )   #所以如果是空資料夾的話，len(folder_list)會少1。
            else:
                #text = self.tree.GetItemText(item)
                #print ' ' * indent, text
                wblink = []
                wblink.append(self.tree.GetItemText(item))
                wblink.append(self.tree.GetPyData(item))
                folder_list.append(wblink)
                
            item, cookie = self.tree.GetNextChild(parent, cookie)
            
        rlist.append(folder_list)
        
        return rlist
        
    def FCopy(self, event):
        self.MyCopyBuffer = []
        #gc.collect()   #? need??       ※you have to test!   效果是沒什麼差，反覆操作記憶體會照樣累積.反正有寫總比沒寫好.. oop mem leak, lel
        
        for item in self.SelectedItemS:
            if self.tree.ItemHasChildren(item):
                self.MyCopyBuffer.append( self.recCollectToList(item) )
            else:
                wblink = []
                wblink.append(self.tree.GetItemText(item))
                wblink.append(self.tree.GetPyData(item))
                self.MyCopyBuffer.append(wblink)
        
    #    if self.mi_check.IsChecked() == False:     #log_MyCopyBuffer內容正常，所以這看起來像是TreeCtrl的連續操作自己crash掉了。
    #        pass
    #        pass
    #        pass
    
#        time.sleep(1)
#        
#        if self.hybridFlag == False:
#            self.operationFlag = False

        if self.mi_check.IsChecked() == True:
            data = wx.TextDataObject()
            data.SetText(str(self.MyCopyBuffer))
            if wx.TheClipboard.Open():
                wx.TheClipboard.SetData(data)
                wx.TheClipboard.Close()
            else:
                #wx.MessageBox("Unable to open the clipboard", "Error")
                pass

        pass
        
    # def ToggleItem(self, item):
        # time.sleep(1.5)
        # self.tree.ToggleItemSelection(item)
        # #time.sleep(1.5)
        # #self.Bind(wx.EVT_TREE_SEL_CHANGED, self.OnSel, self.tree)
        
    def FSetURL(self, event):
        #除非選的只有一個，否則按鈕的功能是一次開啟所有選中的項目。
        if len(self.SelectedItemS) > 1:
            for _item in self.SelectedItemS:
                dat_item_data = self.tree.GetPyData(_item)
                if dat_item_data[2] == 2:
                    webbrowser.open(dat_item_data[1])
        else:
            #self.tree.ToggleItemSelection(self.SelectedItem)
            list_data = self.tree.GetPyData(self.SelectedItem)
            if self.SelectedItem != self.tree.RootItem and list_data[2] == 2:
                list_data[1] = self.tc.GetLineText(0)
                self.tree.SetPyData(self.SelectedItem, list_data)
                pass
        
    # def FAppendItem(self, event):
        # item = self.tree.GetFocusedItem()   #wxTreeCtrl::GetSelection(): this only works with single selection controls
        # pass
        # #self.tree.AppendItem(item, 'i fuck u bitch賤貨', 'http://fuckthemmore.com', 0)
        # self.tree.AppendItem(item, 'i fuck u bitch0', 0)
        # self.tree.AppendItem(item, 'i fuck u bitch1', 1)
        # self.tree.AppendItem(item, 'i fuck u bitch2', 2)
        # #self.tree.AppendItem(item, 'i fuck u bitch2', 2, 2, 'fuck fuck?')
        # #self.tree.SetItemPyData(self.tree.AppendItem(item, u'i fuck u bitch*賤貨1', 2), 'http://fuckthemmore.com')
        # self.tree.SetItemPyData(self.tree.AppendItem(item, unicode('i fuck u bitch*賤貨2', 'utf-8'), 2), 'http://fuckthemmore.com')
        # self.tree.SetItemPyData(self.tree.AppendItem(item, u'i fuck u bitch*賤貨3', 2), ['http://fuckthemmore.com', unicode('str:幹林娘', 'utf-8'), 10])
        # #u_str = b_str.decode('big5')
        # #u_str = unicode(b_str, "big5")
        # #b_str = u_str.encode('big5')
        # #repr() #?
    # #def __str__(self): #for pass
    # #    return str(self.i)
    # #[網路說]：str()出来的值是给人看的。。。repr()出来的值是给python看的，可以通过eval()重新变回一个Python对象。。。
        
    
    # def FInsertItem(self, event):
        # item = self.tree.GetFocusedItem()
        # pass
        # self.tree.SetPyData(self.tree.InsertItem(self.tree.GetItemParent(item), item, "InsertItemName", 2), 'InsertItemData')
    
    # def FInsertItemBefore(self, event):
        # item = self.tree.GetFocusedItem()
        # pass
        # #insert only before the first child
        # self.tree.SetItemPyData(self.tree.InsertItemBefore(self.tree.GetItemParent(item), 0, 'InsertItemBeforeName', 2), 'InsertItemBeforeData')
        
    def getFiletime(self, dt):
        microseconds = int(dt, 16) / 10
        #microseconds = int(dt) / 10    #給int 用
        seconds, microseconds = divmod(microseconds, 1000000)
        days, seconds = divmod(seconds, 86400)

        #return datetime.datetime(1601, 1, 1) + datetime.timedelta(days, seconds, microseconds)
        return datetime.datetime(1601, 1, 1) + datetime.timedelta(days, seconds)
        
    #def OnSel2(self, event):#workaround
    #    self.Bind(wx.EVT_TREE_SEL_CHANGED, self.OnSel, self.tree)
    #    #workaround.
    #    item = event.GetItem()
    #    self.SelectedItem = item
    #    items = self.tree.GetSelections()
    #    self.SelectedItemS = items
    #    try:
    #        if self.tree.ItemHasChildren(item):
    #            self.SelectedFolder = True
    #        else:
    #            self.SelectedFolder = False #空資料夾時也當作選link
    #    except:
    #        pass
    #    #pass
    def OnSel(self, event):
        #print 'OnSel'
        #self.Selected = True    #workaround for SubclassDialog __init__
    
        # if self.sByPass > 0:
            # self.sByPass -= 1
            # return
            
        # pass
        item = event.GetItem()
#        pass
        #items = self.tree.GetSelection()
        #items = self.tree.GetFocusedItem()   #wxTreeCtrl::GetSelection(): this only works with single selection controls
        items = self.tree.GetSelections()
        self.SelectedItemS = items      #for Delete
        #for _i, in items:
        #    pass
        #for i in range(0, len(items)):
        #for i in range(len(items)-1, 0-1, -1):
        #i = len(items) - 1
        #while i > 0-1:
#        for i in range(len(items)):
#            pass
            #i -= 1
        try:
            pyObj = self.tree.GetPyData(item)
#            pass
        
            if item == self.tree.RootItem:
                self.tc.SetValue('')
                self.SelectedItem = item
            else:
                if pyObj[2] == 0:   ##list_data[2]:type 0 = folder
                    self.tc.SetValue('')
                    self.SelectedItem = item
                    
                    self.btn.SetLabel('WriteURL')
                    self.SelectedFolder = True    #workaround for SubclassDialog __init__
                else:
                    if pyObj[2] == 2:   ##list_data[2]:type 2 = web page link
                        self.tc.SetValue(pyObj[1])
                        
                        self.SelectedItem = item    #for Write button
                        
                        parnt = self.tree.GetItemParent(item)
                        indx = 1
                        _item, _cookie = self.tree.GetFirstChild(parnt)
                        while _item:
                            if _item == item:
                                break
                            indx += 1
                            _item, _cookie = self.tree.GetNextChild(parnt, _cookie)
                        
                        self.btn.SetLabel('W:<'+str(indx)+'>')
                        self.SelectedFolder = False    #workaround for SubclassDialog __init__
            
                ##################################換Menu名字
                menu1 = self.mb.GetMenu(self.reservedMenuCount)
                #menu2 = wx.Menu()
                #self.mb.Replace(self.reservedMenuCount, menu2, 'date_added')
                #self.mb.Replace(self.reservedMenuCount, wx.Menu(), pyObj[0])
                #date_str = str( self.getFiletime(hex(0*10)[2:17]) ) #1601年1月1日
                
                
                #print os.environ["TZ"]
                #print time.timezone    ## +8台北是-28800, +9首爾是-32400, 相差3600
                #print time.tzname[0] +':'+ time.tzname[1]
                #date_str = str( self.getFiletime(hex(4053642806023243*10)[2:17]) )     #測試調整時差，會報字尾L長整數無法用int(hex_str, 16)出錯
                #hex_str = str( hex(4053642806023243*10)[2:17] )
                #hex_str = str( hex( 28800000000 *10)[2:17] )    #### <(--要調整時差就在這裡測試.
                ## +28800000000 就是Chrome Bookmarks的+8時區時差@@,這麼剛好被我try到..
                ## 3600000000 = 1 hour
                #TIME_ZONE_GAP = (time.timezone / 3600) * 3600000000
                #hex_str = str( hex( (int(pyObj[0])+28800000000) *10)[2:17] )
                #hex_str = str( hex( (int(pyObj[0])-self.TIME_ZONE_GAP) *10)[2:17] )
                #print hex( (int(pyObj[0])-self.TIME_ZONE_GAP) *10)
                #print hex( (int(pyObj[0])-self.TIME_ZONE_GAP) *10)[2:17] #[2:17]是為了要除掉0x 跟它固定長度的字尾L ...固定長度==
                
                hex_str = hex( (int(pyObj[0])-self.TIME_ZONE_GAP) *10)
                #hex_str = hex( (100000000) *10) #這裡可以測,要多大的數字它才會出現 '除L'
                # pass
                hex_str = hex_str[2:len(hex_str)] ##所以，就直接都除掉0x 跟不論長度的字尾L ==(就完全不折騰了)
                if hex_str.rfind('L') != -1:
                    hex_str = hex_str[0:len(hex_str)-1] #想把L去掉(long)，像是9003aaa2fe4aeeL
                    ##print u'除L'
                # pass
                date_str = str( self.getFiletime( hex_str ) )
                
                
                #date_str = str( self.getFiletime(hex(int(pyObj[0])*10)[2:17]) ) #此不考慮時區
                self.mb.Replace(self.reservedMenuCount, wx.Menu(), date_str)
                del menu1
            
        except:
            pass
        
        
        #準備要開工了(來去寫parser)
        
        # #還是得知道選到的點，child有哪些...
        # if self.tree.ItemHasChildren(item):
            # pass
            # pass
            # pass
            # #------------------------------------------------
            # #(child, cookie) = self.GetFirstChild(item)
            # #while child.IsOk():
            # #    do_something(child)
            # #    (child, cookie) = self.GetNextChild(item, cookie)
            
    def OnDClick(self, event):
        #item = self.tree.GetSelection()
        item = event.GetItem()
        #print("you clicked on", self.tree.item(item,"text"))
    #    pass
        #self.tree.SetItemPyData(event.GetItem(), 'asdata')  #same as SetPyData()
        #url = self.tree.GetPyData(event.GetItem())
        #if url:
        list_data = self.tree.GetPyData(event.GetItem())
        if list_data and list_data[2] == 2: #type = 2 web page link
            #os.system('cmd /c start https://www.google.com.tw/search?sourceid=chrome-psyapi2&ion=1&espv=&ie=UTF-8&q=%E6%AF%8F%E5%BD%93%E6%88%91%E8%BF%B7%E5%A4%B1%E5%9C%A8%E9%BB%91%E5%A4%9C%E9%87%8C')
            #os.system('cmd /c start ' + list_data[1])
            webbrowser.open(list_data[1])
            #os.system('\"%ProgramFiles(x86)%\Google\Chrome\Application\chrome.exe\" \"' + list_data[1] + '\"')
            #os.system('\"C:\Progra~2\Google\Chrome\Application\chrome.exe\" \"' + list_data[1] + '\"')
            
        
    def OnDeleteChildren(self, evt):
        # #item = self.tree.GetSelection()    #wxTreeCtrl::GetSelection(): this only works with single selection controls
        # item = self.tree.GetFocusedItem()
        # if item:
            # self.tree.DeleteChildren(item)
        
        backUp_list = self.SelectedItemS
        if self.mi_check6.IsChecked(): #<huge deletion>
            self.tree.CollapseAll()  
            self.tree.UnselectAll() #try to skip the select show add time per pick
        
        #for item in self.SelectedItemS:
        for item in backUp_list:
            # if item != self.tree.RootItem:
                # self.recDelete(item)
            
            if self.tree.ItemHasChildren(item):
                self.tree.DeleteChildren(item)
                if item != self.tree.RootItem:
                    self.tree.Delete(item)
            else:
                if item != self.tree.RootItem:
                    self.tree.Delete(item)
        
        gc.collect()
        
    #    if self.hybridFlag == False:
    #        self.operationFlag = False
#        self.operationFlag = False
        #pass

#    def PrintAllItems(self, parent, indent=0):
#        text = self.tree.GetItemText(parent)
#        pass
#        indent += 4
#        item, cookie = self.tree.GetFirstChild(parent)
#        while item:
#            if self.tree.ItemHasChildren(item):
#                self.PrintAllItems(item, indent)
#            else:
#                text = self.tree.GetItemText(item)
#                pass
#            item, cookie = self.tree.GetNextChild(parent, cookie)

    
    def AddTreeNodes(self, parentItem, items):
        for item in items:
            if type(item) == str:
                newItem = self.tree.AppendItem(parentItem, item)
                self.tree.SetItemPyData(newItem, None)
                self.tree.SetItemImage(newItem, self.fileidx,wx.TreeItemIcon_Normal)
            else:
                newItem = self.tree.AppendItem(parentItem, item[0])
                self.tree.SetItemPyData(newItem, None)
                self.tree.SetItemImage(newItem, self.fldridx,wx.TreeItemIcon_Normal)
                self.tree.SetItemImage(newItem, self.fldropenidx,wx.TreeItemIcon_Expanded)
   
                self.AddTreeNodes(newItem, item[1])

#def dump(obj):
#   for attr in dir(obj):
#       if hasattr( obj, attr ):
#           pass

gframe = 0

#def gloop():
#    while 1:
#    
#        #要用它自訂的:
#        #wx.WXK_DELETE
#        #wx.WXK_CONTROL
#        #wx.WXK_SHIFT
#        #wx.WXK_ALT
#        #wx.WXK_F1
#        
#        #print currentWindowHandle
#        if currentWindowHandle == w.GetForegroundWindow():
#            if wx.GetKeyState(wx.WXK_DELETE):
#                #print 'K(Del)'
#                gframe.OnDeleteChildren(0)
#                time.sleep(0.5) #timeout
#        #    if wx.GetKeyState(wx.WXK_CONTROL) and wx.GetKeyState(ord('C')):
#        #        #print 'K(Copy)'
#        #        gframe.FCopy(0)
#        #        time.sleep(0.5) #timeout
#        #    if wx.GetKeyState(wx.WXK_CONTROL) and wx.GetKeyState(ord('x')):
#        #        #print 'K(Cut)'
#        #        gframe.FCut(0)
#        #        time.sleep(0.5) #timeout
#        #    if wx.GetKeyState(wx.WXK_CONTROL) and wx.GetKeyState(ord('V')):
#        #        #print 'K(Paste)'
#        #        gframe.FPaste(0)
#        #        time.sleep(0.5) #timeout
#        
#        time.sleep(0.1)

if __name__ == "__main__":

    user32 = windll.LoadLibrary("user32.dll")

    #os.system('cmd /c start http://www.google.com')
    app = wx.App(redirect=True)   # Error messages go to popup window
    #top = TestTreeCtrlPanel("<<project>>")
    #top.Show()
    #dump(wx.Frame)
    gframe = TestFrame()
    gframe.Show()
    currentWindowHandle = user32.GetActiveWindow()
    
    #list1 = ["One", "Two", "Three"]
    # list1.insert(2, "Two 1/2")
    # list1.insert(0, "Two 1/2")
    # list1.insert(5, "Two 1/2")
    # pass
    #str = str(list1)
    #print str[3:6]
    #listS = eval(str)  #np++還有自動註解@@
    #print listS[2]
    
    app.MainLoop()
