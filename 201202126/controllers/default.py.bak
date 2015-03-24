# -*- coding: utf-8 -*-
# this file is released under public domain and you can use without limitations

#########################################################################
## This is a sample controller
## - index is the default action of any application
## - user is required for authentication and authorization
## - download is for downloading files uploaded in the db (does streaming)
## - call exposes all registered services (none by default)
#########################################################################

import datetime
from gluon.tools import Crud

def index():
    """
    example action using the internationalization operator T and flash
    rendered by views/default/index.html or views/generic.html

    if you need a simple wiki simple replace the two lines below with:
    return auth.wiki()
    """
    if auth.user:
        redirect(URL(r=request , f='homepage' ))
    #elif auth.user.username == 'admin':
     #   redirect(URL(r=reques , f='homepage'))
    response.flash = T("Welcome to web2py!")
    return dict(message=T('Hello World'))


def user():
    """
    exposes:
    http://..../[app]/default/user/login
    http://..../[app]/default/user/logout
    http://..../[app]/default/user/register
    http://..../[app]/default/user/profile
    http://..../[app]/default/user/retrieve_password
    http://..../[app]/default/user/change_password
    http://..../[app]/default/user/manage_users (requires membership in 
    use @auth.requires_login()
        @auth.requires_membership('group name')
        @auth.requires_permission('read','table name',record_id)
    to decorate functions that need access control
    """
    return dict(form=auth())

@cache.action()
def download():
    """
    allows downloading of uploaded files
    http://..../[app]/default/download/[filename]
    """
    return response.download(request, db)


def call():
    """
    exposes services. for example:
    http://..../[app]/default/call/jsonrpc
    decorate with @services.jsonrpc the functions to expose
    supports xml, json, xmlrpc, jsonrpc, amfrpc, rss, csv
    """
    return service()


@auth.requires_signature()
def data():
    """
    http://..../[app]/default/data/tables
    http://..../[app]/default/data/create/[table]
    http://..../[app]/default/data/read/[table]/[id]
    http://..../[app]/default/data/update/[table]/[id]
    http://..../[app]/default/data/delete/[table]/[id]
    http://..../[app]/default/data/select/[table]
    http://..../[app]/default/data/search/[table]
    but URLs must be signed, i.e. linked with
      A('table',_href=URL('data/tables',user_signature=True))
    or with the signed load operator
      LOAD('default','data.load',args='tables',ajax=True,user_signature=True)
    """
    return dict(form=crud())

crud = Crud(db)
      
def homepage():
    user = db(db.auth_user.id == auth.user.id).select(db.auth_user.username)
    return dict(user=user)
        
def userpost():
    user = db(db.usernews.id>0).select(db.usernews.id)
    form = SQLFORM(db.usernews,deletable=True,fields=['cat','heading','link'])
    #INCLUDE CATEGORY LATER
    form.vars.points = 100
    form.vars.dat = datetime.datetime.today()
    form.vars.userid = auth.user.id		
    form.vars.name = auth.user.username	
    if form.accepts(request.vars,session):
        redirect(URL(r=request , f='homepage'))
    return dict(form=form)
    
def cment():
    a=request.args[0]
    form =  SQLFORM(db.comments , deletable=True , fields=['matter'])
    x = db(a == db.usernews.id).select(db.usernews.heading)
    form.vars.heading = x[0].heading
    form.vars.newsid = a
    form.vars.userid = auth.user.id
    form.vars.dat=datetime.datetime.today()
    dc = db((db.comments.newsid == db.usernews.id) & (db.usernews.id == a)).select(db.usernews.id)
    if form.accepts(request.vars,session):
        redirect(URL(r=request , f='homepage'))
    return dict(form=form , dc=dc)
def delpostadmin():
    y =request.args[0]
    db(y == db.usernews.id).delete()
    db(y == db.comments.newsid).delete()
    db(y == db.point.newsid).delete()
    redirect(URL(r=request , f='homepage'))
def admincat():
    form = SQLFORM(db.categories , deletable = True , fields=['categories'])
    if form.accepts(request.vars , session):
        redirect(URL(r=request , f='admincat'))
    return dict(form=form)
def accounts():
    return dict()
def delacc():
    x = request.args[0]
    db(x == db.comments.userid).delete()
    db(x == db.usernews.userid).delete()
    db(x == db.point.userid).delete()
    db(x == db.auth_user.id).delete()
    redirect(URL(r=request , f="accounts")) 
def delcat():
    y = request.args[0]
    f = db(db.categories.id == y).select(db.categories.categories)
    db(y == db.categories.id).delete()
    x = db(f[0].categories == db.usernews.cat).select(db.usernews.id)
    db(x[0].id == db.comments.newsid).delete()
    db(x[0].id == db.point.newsid).delete()
    db(y == db.usernews.cat).delete()
    redirect(URL(r=request , f="admincat"))
def viewpost():
    return dict()
def usredtpst():
    a = request.args[0]
    comments=db(db.usernews.id == a).select(db.usernews.id)
    record = comments[0].id
    form = crud.update(db.usernews , record ,fields=['cat','heading','link'],deletable=False,next = URL(r=request, f='viewpost'))
    #db(db.comments.id == a).update(dat = datetime.datetime.today())
    return dict(form=form)
def usrdelpst():
    y =request.args[0]
    db(y == db.usernews.id).delete()
    db(y == db.comments.newsid).delete()
    db(y == db.point.newsid).delete()
    redirect(URL(r=request , f="viewpost"))
def delete():
    a = request.args[0]
    db(db.comments.id == a).delete()
def choice():
    ids = request.args[0]
    iid = db((db.categories.id == ids)).select(db.categories.categories)
    return dict(iid=iid)
def calculate():
    uid = request.args[0]
    nid = request.args[1]
    ag = request.args[2]
    if ag == '0':
        db((db.point.userid == uid) & (db.point.newsid == nid)).update(liked = 0)
        db((db.point.userid == uid) & (db.point.newsid == nid)).update(disliked = 0)
    elif ag == '1':
        db((db.point.userid == uid) & (db.point.newsid == nid)).update(liked = 1)
        db((db.point.userid == uid) & (db.point.newsid == nid)).update(disliked = 0)
    elif ag == '-1':
        db((db.point.userid == uid) & (db.point.newsid == nid)).update(liked = 0)
        db((db.point.userid == uid) & (db.point.newsid == nid)).update(disliked = 1)
    redirect(URL(r=request , f="homepage"))
    return dict()
def cal1():
    uid = request.args[0]
    nid = request.args[1]
    ag = request.args[2]
    if ag == '0':
        db((db.point.userid == uid) & (db.point.newsid == nid)).update(liked = 0)
        db((db.point.userid == uid) & (db.point.newsid == nid)).update(disliked = 0)
    elif ag == '1':
        db((db.point.userid == uid) & (db.point.newsid == nid)).update(liked = 1)
        db((db.point.userid == uid) & (db.point.newsid == nid)).update(disliked = 0)
    elif ag == '-1':
        db((db.point.userid == uid) & (db.point.newsid == nid)).update(liked = 0)
        db((db.point.userid == uid) & (db.point.newsid == nid)).update(disliked = 1)
    f = db((nid == db.usernews.id) & (db.categories.categories == db.usernews.cat)).select(db.categories.id)
    redirect(URL(r=request , f="choice" , args=f[0].id))
    return dict()
'''{{count1 = db(id[i].id  == db.point.newsid).select(db.point.like)}}
    {{for z in range(len(count1)):}}
    {{if count1[z].like == 1:}}
    {{cnt1+=1}}
    {{pass}}
    {{pass}}
    {{count2 = db(id[i].id == db.point.newsid).select(db.point.dislike)}}
    {{if count2[z].dislike == 1:}}
    {{cnt2+=1}}
    {{pass}}
    {{pass}}
    Paste in homepage.html
    {{db(ind['id'] == db.usernews.id).update(points = 100)}}
    {{cnt1=0}}
    {{cnt2=0}}
    {{count1 = db(ind['id']  == db.point.newsid).select(db.point.liked)}}
    {{for z in range(len(count1)):}}
    {{if count1[z].liked == 1:}}
    {{cnt1+=1}}
    {{pass}}
    {{pass}}
    {{count2 = db(ind['id'] == db.point.newsid).select(db.point.disliked)}}
    {{for z in range(len(count2)):}}
    {{if count2[z].disliked == 1:}}
    {{cnt2+=1}}
    {{pass}}
    {{pass}}
    {{x = 0}}
    {{x = cnt1*5 - cnt2*3}}
    {{fff = db(ind['id'] == db.usernews.id).select(db.usernews.points)}}
    {{x = x + fff[0].points}}
    {{db(ind['id'] == db.usernews.id).update(points = x)}}'''
