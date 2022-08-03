```python
import hou
from PySide2 import QtWidgets
from PySide2 import QtCore
from PySide2 import QtSvg
from PySide2 import QtGui


class BasicPopup(QtWidgets.QDialog):
    def __init__(self):
        super(BasicPopup, self).__init__(
            hou.qt.mainWindow(),
        )
        #make window relative to size of parent
        self.relX = .05
        self.relY = .05
        self.setWindowModality(QtCore.Qt.ApplicationModal)
        width = hou.qt.mainWindow().width()
        height = hou.qt.mainWindow().height()
        self.width =  width * self.relX
        self.height = height * self.relY

        self.configure_dialog()
        self.widgets()
        self.layouts()
        self.connections()
    #release the searchInputs hold on the keyboard whenever the window is closed
    def closeEvent(self, event):
        # self.searchInput.releaseKeyboard()
        print("closed")

    def configure_dialog(self)->None:
        self.setMinimumWidth(self.width)
        self.setMinimumHeight(self.height)
        self.setAttribute(QtCore.Qt.WA_TranslucentBackground)
        # self.setWindowFlags(self.windowFlags() |  QtCore.Qt.WindowStaysOnTopHint |  QtCore.Qt.X11BypassWindowManagerHint)
        self.setWindowFlags( QtCore.Qt.X11BypassWindowManagerHint| QtCore.Qt.Popup | QtCore.Qt.FramelessWindowHint)
        # self.setWindowFlags(QtCore.Qt.WindowStaysOnTopHint |  QtCore.Qt.X11BypassWindowManagerHint| QtCore.Qt.Popup | QtCore.Qt.FramelessWindowHint)
        self.centralwidget = QtWidgets.QWidget(self)

    def connections(self)->None:
        self.closeButton.clicked.connect(self.test)
        
    def test(self, event)->None:
        print("test")
    
    def layouts(self)->None:
        self.container = QtWidgets.QWidget(self)
        self.container.setMinimumWidth(self.width)
        self.container.setMinimumHeight(self.height)
        self.hBox = QtWidgets.QVBoxLayout(self)
        self.hBox.addWidget(self.closeButton)

        radius = 5
        self.container.setStyleSheet(
            """
            background:rgba(18, 18, 18, .7);
            border-top-left-radius:{0}px;
            border-bottom-left-radius:{0}px;
            border-top-right-radius:{0}px;
            border-bottom-right-radius:{0}px;
            """.format(radius)
        )
    
    #positions the popup at the mouse location see https://gist.github.com/justinfx/1951709
    def showEvent(self, event):
        geom = self.frameGeometry()
        geom.moveCenter(QtGui.QCursor.pos())
        self.setGeometry(geom)
        super(BasicPopup, self).showEvent(event)
    
    def widgets(self)->None:
        self.closeButton = QtWidgets.QPushButton("Close")


#hacky way of deleting old windows dont create multiple windows
for widget in hou.qt.mainWindow().children():
    try:
        if widget.isSearchWindow:
            widget.close()
    except:
        None

popup = BasicPopup()
popup.setModal(True)
popup.show()
```
