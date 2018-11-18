unit main;

{$mode objfpc}{$H+}

interface

uses
  Classes, SysUtils, FileUtil, TAGraph, TASources, Forms, Controls, Graphics,
  Dialogs, StdCtrls, blcksock, TACustomSource, TASeries;

type

  { TMainForm }

  TMainForm = class(TForm)
    Button1: TButton;
    Chart1: TChart;
    WaveLineSeries: TLineSeries;
    Memo1: TMemo;
    procedure Button1Click(Sender: TObject);
    procedure UserDefinedChartSource1GetChartDataItem(
      ASource: TUserDefinedChartSource; AIndex: Integer;
      var AItem: TChartDataItem);
  private
    { private declarations }
  public
    { public declarations }
  end;

var
  MainForm: TMainForm;

implementation

{$R *.lfm}

{ TMainForm }

procedure TMainForm.Button1Click(Sender: TObject);
var
  sock: TTCPBlockSocket;
  buffer: String = '';
  rx : String = '';
  dt : String = '';
  sl: TStringList;
  n : integer;
begin
  sock := TTCPBlockSocket.Create;

      sock.Connect('192.168.1.100', '5555');
      if sock.LastError <> 0 then
      begin
          showmessage('No cukierek!');
          exit;
      end;
      sock.SendString(':waveform:source CHANNEL1'#13#10 );
      sock.SendString(':waveform:mode NORMAL'#13#10 );
      sock.SendString(':waveform:form ASCII'#13#10 );
      sock.SendString(':waveform:data?'#13#10 );
      repeat
          buffer := sock.RecvPacket(500);
          memo1.lines.add( buffer );
         rx :=  rx + buffer;
      until buffer = '';
      dt := Copy(rx, 12);
      sl := TstringList.Create;
      sl.StrictDelimiter := true;
      sl.DelimitedText := dt;
      self.WaveLineSeries.Clear;
      Chart1.LeftAxis.Range.Min := -10;
      Chart1.LeftAxis.Range.Max := 10;
      for n := 0 to sl.Count-1 do
      begin
        self.WaveLineSeries.AddXY(n, StrToFloat( sl.Strings[ n ]) );
      end;
end;

procedure TMainForm.UserDefinedChartSource1GetChartDataItem(
  ASource: TUserDefinedChartSource; AIndex: Integer; var AItem: TChartDataItem);
begin

end;

end.

