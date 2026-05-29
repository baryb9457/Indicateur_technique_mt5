# Indicateur_technique_mt5
C'est un indicateur technique sur la plate-forme de MT5, utiliser pour analyser les marchés financiers

<img width="837" height="456" alt="image d&#39;indicateur" src="https://github.com/user-attachments/assets/64ae07a2-e0fb-4713-b4d4-babe1dba6c6e" />


//+------------------------------------------------------------------+
//| Dashboard_Deplacable.//| KAM //+------------------------------------------------------------------+

#property copyright "KAM"
#property link ""
#property version "2.00"
#property indicator_chart_window
#property indicator_buffers 0
#property indicator_plots 0
// --- Paramètres d'entrée
input int InpMAPeriod = 21;
// Période de la Moyenne Mobile
input ENUM_MA_METHOD InpMAMethod = MODE_EMA;
// Type de Moyenne Mobile
input int InpRangePips = 15;
// Seuil de Range (en points)
input color ColorUp = clrDarkGreen;
// Couleur Hausse (Fond)
input color ColorDown = clrMaroon;
// Couleur Baisse (Fond)
input color ColorRange = clrChocolate;
// Couleur Range (Fond)
input color ColorText = clrWhite;
// Couleur du Texte
input color ColorBg = clrBlack;
// Couleur de fond du panneau
// --- Variables globales

ENUM_TIMEFRAMES timeframes[] = {PERIOD_M5,
PERIOD_M30, PERIOD_H1, PERIOD_H4, PERIOD_D1};
string tfNames[] = {"M5", "M30", "H1", "H4", "D1"};
int tfCount = 5;
int maHandles[5];
// Noms des objets graphiques
string bgName = "Dash_Bg_Panel";
//+------------------------------------------------------------------+
//| Custom indicator initialization function //+------------------------------------------------------------------+
int OnInit()
{
// Création du fond du tableau (Rectangle de base en haut // On utilise CORNER_LEFT_UPPER pour faciliter les calculs CreateObjectRect(bgName, 800, 50, 180, 145,
ColorBg);
// Initialisation des boutons pour chaque TF
for(int i=0; i<tfCount; i++)
{
string btnName = "Dash_Btn_" + tfNames[i];
CreateObjectButton(btnName, ColorText,
clrGray);
maHandles[i] = iMA(_Symbol, timeframes[i],
InpMAPeriod, 0, InpMAMethod, PRICE_CLOSE);
if(maHandles[i] == INVALID_HANDLE) return(
INIT_FAILED);

}
// Premier alignement des boutons sur le rectangle
UpdatePanelPositions();
return(INIT_SUCCEEDED);
}
//+------------------------------------------------------------------+
//| Custom indicator deinitialization function //+------------------------------------------------------------------+
void OnDeinit(const int reason)
{
ObjectDelete(0, bgName);
for(int i=0; i<tfCount; i++)
{
ObjectDelete(0, "Dash_Btn_" + tfNames[i]);
if(maHandles[i] != INVALID_HANDLE)
IndicatorRelease(maHandles[i]);
}
ChartRedraw();
}
//+------------------------------------------------------------------+
//| Custom indicator iteration function //+------------------------------------------------------------------+
int OnCalculate(const int rates_total, const int
prev_calculated, const int begin, const double &
price[])

{
string symbol = _Symbol;
// S'assurer que les boutons suivent toujours le rectangle UpdatePanelPositions();
for(int i=0; i<tfCount; i++)
{
string trendStr = "";
color trendColor = clrGray;
GetTrend(symbol, timeframes[i], maHandles[i],
trendStr, trendColor);
string btnName = "Dash_Btn_" + tfNames[i];
ObjectSetString(0, btnName, OBJPROP_TEXT,
tfNames[i] + " | " + trendStr);
ObjectSetInteger(0, btnName, OBJPROP_BGCOLOR,
trendColor);
ObjectSetInteger(0, btnName, OBJPROP_STATE,
false);
}
ChartRedraw();
return(rates_total);
}
//+------------------------------------------------------------------+
//| Gestionnaire d'événements (Clics et Déplacements) //+------------------------------------------------------------------+

void OnChartEvent(const int id, const long &lparam,
const double &dparam, const string &sparam)
{
// 1. Gestion du déplacement (Drag & Drop du fond)
if(id == CHARTEVENT_OBJECT_DRAG)
{
if(sparam == bgName)
{
UpdatePanelPositions();
// Recalcule la position de tous les boutons en temps réel
ChartRedraw();
}
}
// 2. Gestion du clic sur les boutons
if(id == CHARTEVENT_OBJECT_CLICK)
{
for(int i=0; i<tfCount; i++)
{
string btnName = "Dash_Btn_" + tfNames[i];
if(sparam == btnName)
{
ChartSetSymbolPeriod(0, _Symbol,
timeframes[i]);
ObjectSetInteger(0, btnName,
OBJPROP_STATE, false);
ChartRedraw();
break;
}
}
}
}

//+------------------------------------------------------------------+
//| Synchronisation des positions des boutons sur le rectangle //+------------------------------------------------------------------+
void UpdatePanelPositions()
{
// On récupère les coordonnées actuelles du rectangle de int currentX = (int)ObjectGetInteger(0, bgName,
OBJPROP_XDISTANCE);
int currentY = (int)ObjectGetInteger(0, bgName,
OBJPROP_YDISTANCE);
int yOffset = currentY + 10;
int buttonWidth = 160;
int buttonHeight = 22;
for(int i=0; i<tfCount; i++)
{
string btnName = "Dash_Btn_" + tfNames[i];
// On aligne chaque bouton à l'intérieur du rectangle
ObjectSetInteger(0, btnName,
OBJPROP_XDISTANCE, currentX + 10);
ObjectSetInteger(0, btnName,
OBJPROP_YDISTANCE, yOffset);
ObjectSetInteger(0, btnName, OBJPROP_XSIZE,
buttonWidth);
ObjectSetInteger(0, btnName, OBJPROP_YSIZE,
buttonHeight);
yOffset += 25; // Espace entre chaque bouton

}
}
//+------------------------------------------------------------------+
//| Logique de tendance //+------------------------------------------------------------------+
void GetTrend(string sym, ENUM_TIMEFRAMES tf, int
handle, string &trendText, color &bgColor)
{
if(handle == INVALID_HANDLE) { trendText =
"Erreur"; bgColor = clrRed; return; }
double maValues[3];
if(CopyBuffer(handle, 0, 0, 3, maValues) < 3) {
trendText = "Loading..."; bgColor = clrGray; return
; }
double closeValues[2];
if(CopyClose(sym, tf, 0, 2, closeValues) < 2) {
trendText = "Loading..."; bgColor = clrGray; return
; }
double currentMA = maValues[1];
double previousMA = maValues[0];
double currentClose = closeValues[1];
double diff = (currentMA - previousMA);
double threshold = InpRangePips *
SymbolInfoDouble(sym, SYMBOL_POINT);
if(MathAbs(diff) <= threshold)
{
trendText = "RANGE ⇄";
bgColor = ColorRange;

}
else if(diff > threshold && currentClose >
currentMA)
{
trendText = "HAUSSE ⇄";
bgColor = ColorUp;
}
else if(diff < -threshold && currentClose <
currentMA)
{
trendText = "BAISSE ⇄";
bgColor = ColorDown;
}
else
{
trendText = "NEUTRE ⇄";
bgColor = ColorRange;
}
}
//+------------------------------------------------------------------+
//| Fonctions de création d'objets //+------------------------------------------------------------------+
void CreateObjectRect(string name, int x, int y,
int width, int height, color bg_color)
{
if(ObjectFind(0, name) < 0)
{
ObjectCreate(0, name, OBJ_RECTANGLE_LABEL, 0,
0, 0);
ObjectSetInteger(0, name, OBJPROP_CORNER,
CORNER_LEFT_UPPER);
// Changé pour le déplacement libre

ObjectSetInteger(0, name, OBJPROP_XDISTANCE,
x);
ObjectSetInteger(0, name, OBJPROP_YDISTANCE,
y);
ObjectSetInteger(0, name, OBJPROP_XSIZE,
width);
ObjectSetInteger(0, name, OBJPROP_YSIZE,
height);
ObjectSetInteger(0, name, OBJPROP_BGCOLOR,
bg_color);
ObjectSetInteger(0, name, OBJPROP_BORDER_TYPE
, BORDER_FLAT);
ObjectSetInteger(0, name, OBJPROP_BACK, false
);
ObjectSetInteger(0, name, OBJPROP_SELECTABLE,
true); // RENDU SÉLECTIONNABLE POUR GLISSER
ObjectSetInteger(0, name, OBJPROP_HIDDEN,
false);
}
}
void CreateObjectButton(string name, color
txt_color, color bg_color)
{
if(ObjectFind(0, name) < 0)
{
ObjectCreate(0, name, OBJ_BUTTON, 0, 0, 0);
ObjectSetInteger(0, name, OBJPROP_CORNER,
CORNER_LEFT_UPPER);
ObjectSetString(0, name, OBJPROP_FONT,
"Arial");
ObjectSetInteger(0, name, OBJPROP_FONTSIZE,
10);
ObjectSetInteger(0, name, OBJPROP_COLOR,
txt_color);

ObjectSetInteger(0, name, OBJPROP_BGCOLOR,
bg_color);
ObjectSetInteger(0, name,
OBJPROP_BORDER_COLOR, clrSilver);
ObjectSetInteger(0, name, OBJPROP_SELECTABLE,
false); // Le bouton ne bouge pas seul
ObjectSetInteger(0, name, OBJPROP_STATE,
false);
ObjectSetInteger(0, name, OBJPROP_HIDDEN,
false);
}
}
