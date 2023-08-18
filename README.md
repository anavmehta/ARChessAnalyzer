# ARChessAnalyzer

- Stockfish 9.0
- OpenCV 4.3
- Free chess piece/board icons used from iconfinder.com under Creative Commons (Attribution 2.5 Generic)
The ARChessAnalyzer app provides live Augmented Reality feedback of the next best move of a chess position on a physical chessboard.

A player starts by pointing the app live capture to a chessboard. The board position is then determined using a mix of vision and image processing techniques and prediction from machine learning models of pieces and the board. Using the board position, a chess engine computes the next best move and then a "chess diagram" of the position and the move is superimposed on the chessboard. The Augmented Reality feedback to the player takes between 2-3.5sec. By default, white moves first and alternates in subsequent moves.


The following is the detection pipeline:

1. Detecting the chessboard: A CreateML image classifier determines the presence of a chessboard. The model was trained using chessboard and other gameboards ("NotaChessboard") photos.

2. Segmenting the chessboard into 64 squares using OpenCV.
a) Convert to a gray image.
b) Remove noise using Gaussian 3x3 filter.
c) Detect edges using auto Canny.
d) Detect lines using Hough transform and separate horizontal and vertical lines.
e) Compute intersection points.
f) Form clusters using Kmeans.
g) Calculate the bounding box and do a perspective shift.
h) Use error criteria to reject - the number of lines/points or aspect ratio of the chessboard.
i) Segment into 64 (227x227) image squares with error expansion.

3) Predicting the chessboard position from the 64 segmented images. Approximately 200+ augmented photos (flip/rotation/crop) of each of 13 labels (black and white, pawn, rook, knight, bishop, queen, king, and empty) were used for CaffeNet training.
a) The piece occupancy of each of 8x8 image squares is predicted using the CaffeNet/CoreML model.
b) A FEN ((Forsyth-Edwards Notation) board position string is generated.

4) Analyzing and determining the next best move from the FEN position string using Stockfish An iOS pod StockFish framework was used.
a) StockFish computes the best move from the position.
b) The AR chess diagram of the position and move is overlayed on the board for player feedback.

Repeat to step 1.  
