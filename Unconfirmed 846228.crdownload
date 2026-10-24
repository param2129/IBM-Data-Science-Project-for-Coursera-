from pathlib import Path
import pandas as pd
import plotly.express as px
from dash import Dash, dcc, html, Input, Output

p=Path("spacex_launch_data.csv")
if not p.exists():
    raise FileNotFoundError("Run SpaceX_Capstone_Analysis.ipynb first to create spacex_launch_data.csv")
df=pd.read_csv(p)
df["year"]=pd.to_numeric(df["year"],errors="coerce")
years=sorted(df.year.dropna().astype(int).unique())
app=Dash(__name__); app.title="SpaceX Launch Analytics"
app.layout=html.Div([
 html.H1("SpaceX Launch Analytics Dashboard",style={"textAlign":"center"}),
 html.Label("Report type"),
 dcc.Dropdown(id="kind",options=[{"label":"Overall Statistics","value":"all"},{"label":"Yearly Statistics","value":"year"}],value="all",clearable=False),
 html.Label("Year"),
 dcc.Dropdown(id="year",options=[{"label":str(y),"value":y} for y in years],value=years[-1] if years else None,clearable=False),
 html.Div(id="charts",style={"display":"flex","flexWrap":"wrap"})
],style={"fontFamily":"Arial","margin":"24px"})
@app.callback(Output("year","disabled"),Input("kind","value"))
def disable_year(kind): return kind!="year"
@app.callback(Output("charts","children"),[Input("kind","value"),Input("year","value")])
def render(kind,year):
 d=df[df.year==year] if kind=="year" and year is not None else df
 byyear=df.groupby("year",as_index=False).agg(launches=("success_int","size"))
 bysite=d.groupby("launchpad",as_index=False).agg(success_rate=("success_int","mean"),launches=("success_int","size"))
 byrocket=d.groupby("rocket",as_index=False).agg(launches=("success_int","size"))
 payload=d.dropna(subset=["payload_mass_kg","success_int"])
 figs=[
  px.line(byyear,x="year",y="launches",markers=True,title="Launches by Year"),
  px.pie(d,names="success_int",title="Launch Outcome Distribution"),
  px.bar(bysite,x="launchpad",y="success_rate",title="Observed Success Rate by Launch Site"),
  px.scatter(payload,x="payload_mass_kg",y="success_int",color="rocket",hover_data=["launchpad","orbit","year"],title="Payload Mass vs Outcome"),
  px.bar(byrocket,x="rocket",y="launches",title="Launch Count by Rocket")
 ]
 return [html.Div(dcc.Graph(figure=f),style={"width":"48%","minWidth":"420px"}) for f in figs]
if __name__=="__main__": app.run(debug=True)
