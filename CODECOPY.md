Imports System.Windows.Forms.VisualStyles.VisualStyleElement.ProgressBar
'NEED TO ADD:
'checks and checkmate
Public Class Form1
    Dim pieceArr(8, 8) As String
    Dim highlightArr(8, 8) As String

    Dim blackAttackArr(8, 8) As Boolean
    Dim whiteAttackArr(8, 8) As Boolean

    Dim blackPieces(6) As String
    Dim whitePieces(6) As String

    Dim whiteTurn As Boolean = True
    Dim whiteCanCastleKing As Boolean = True
    Dim whiteCanCastleQueen As Boolean = True
    Dim blackCanCastleKing As Boolean = True
    Dim blackCanCastleQueen As Boolean = True

    Dim blackEnPassantPieceList As New List(Of Integer())
    Dim blackEnPassantPos(2) As Integer
    Dim whiteEnPassantPieceList As New List(Of Integer())
    Dim whiteEnPassantPos(2) As Integer



    Private Sub Form1_Load(sender As Object, e As EventArgs) Handles MyBase.Load
        blackPieces = {"♜", "♞", "♝", "♛", "♚", "♟︎"}
        whitePieces = {"♖", "♘", "♗", "♕", "♔", "♙"}
        For i = 0 To 7
            For j = 0 To 7
                highlightArr(i, j) = ""

            Next
        Next
        Dim board As New TableLayoutPanel
        With board
            .RowCount = 8
            .ColumnCount = 8
            .Size = New Size(10000, 10000)
            .Name = "board"
        End With
        pieceArr = {{"♜", "♞", "♝", "♛", "♚", "♝", "♞", "♜"}, {"♟︎", "♟︎", "♟︎", "♟︎", "♟︎", "♟︎", "♟︎", "♟︎"}, {"", "", "", "", "", "", "", ""}, {"", "", "", "", "", "", "", ""}, {"", "", "", "", "", "", "", ""}, {"", "", "", "", "", "", "", ""}, {"♙", "♙", "♙", "♙", "♙", "♙", "♙", "♙"}, {"♖", "♘", "♗", "♕", "♔", "♗", "♘", "♖"}}
        For i = 0 To 7
            For j = 0 To 7
                Dim b As New Button
                With b
                    .Text = pieceArr(i, j)
                    .Font = New Font("Segoe UI", 22)

                    .Name = i & j
                    .Size = New Size(50, 50)
                    If pieceArr(i, j) = "H" Then
                        .BackColor = Color.Red
                    End If
                End With
                AddHandler b.Click, AddressOf pieceClicked
                board.Controls.Add(b)
            Next
        Next
        Controls.Add(board)
        updateBoard(board)
    End Sub
    Dim lastPieceClicked As String = "00"
    Sub pieceClicked(sender As Button, e As EventArgs)
        Dim ypos As Integer = CInt(sender.Name(0).ToString)
        Dim xpos As Integer = CInt(sender.Name(1).ToString)
        Dim lastypos As Integer = CInt(lastPieceClicked(0).ToString)
        Dim lastxpos As Integer = CInt(lastPieceClicked(1).ToString)
        Dim isBlackPiece As Boolean
        Dim isWhitePiece As Boolean

        If highlightArr(ypos, xpos) = "H" Then
            Dim pieceString As String = pieceArr(lastypos, lastxpos)
            For i = 0 To 7
                For j = 0 To 7
                    If highlightArr(i, j) = "H" Then
                        highlightArr(i, j) = ""
                    End If
                Next
            Next

            'CASTLING
            If pieceString = "♔" Then
                whiteCanCastleKing = False
                whiteCanCastleQueen = False
            End If
            If pieceString = "♚" Then
                blackCanCastleKing = False
                blackCanCastleQueen = False
            End If
            If pieceString = "♜" And lastypos = 0 And lastxpos = 0 Then
                blackCanCastleQueen = False
            End If
            If pieceString = "♜" And lastypos = 0 And lastxpos = 7 Then
                blackCanCastleKing = False
            End If
            If pieceString = "♖" And lastypos = 7 And lastxpos = 0 Then
                whiteCanCastleQueen = False
            End If
            If pieceString = "♖" And lastypos = 7 And lastxpos = 7 Then
                whiteCanCastleKing = False
            End If

            If pieceString = "♔" And xpos = 6 Then
                pieceArr(7, 7) = ""
                pieceArr(7, 5) = "♖"
            End If
            If pieceString = "♔" And xpos = 2 Then
                pieceArr(7, 0) = ""
                pieceArr(7, 3) = "♖"
            End If

            If pieceString = "♚" And xpos = 6 Then
                pieceArr(0, 7) = ""
                pieceArr(0, 5) = "♜"
            End If
            If pieceString = "♚" And xpos = 2 Then
                pieceArr(0, 0) = ""
                pieceArr(0, 3) = "♜"
            End If


            'EN PASSANT
            blackEnPassantPieceList.Clear()
            whiteEnPassantPieceList.Clear()
            Try
                If pieceString = "♙" And blackPieces.Contains(pieceArr(ypos, xpos + 1)) Then
                    blackEnPassantPieceList.Add({ypos, xpos + 1})
                    blackEnPassantPos = {ypos + 1, xpos}
                End If
            Catch ex As Exception

            End Try
            Try
                If pieceString = "♙" And blackPieces.Contains(pieceArr(ypos, xpos - 1)) Then
                    blackEnPassantPieceList.Add({ypos, xpos - 1})
                    blackEnPassantPos = {ypos + 1, xpos}
                End If
            Catch ex As Exception

            End Try
            Try
                If pieceString = "♟︎" And whitePieces.Contains(pieceArr(ypos, xpos + 1)) Then
                    whiteEnPassantPieceList.Add({ypos, xpos + 1})
                    whiteEnPassantPos = {ypos - 1, xpos}
                End If
            Catch ex As Exception

            End Try
            Try
                If pieceString = "♟︎" And whitePieces.Contains(pieceArr(ypos, xpos - 1)) Then
                    whiteEnPassantPieceList.Add({ypos, xpos - 1})
                    whiteEnPassantPos = {ypos - 1, xpos}
                End If
            Catch ex As Exception

            End Try

            If pieceString = "♙" And ypos = whiteEnPassantPos(0) And xpos = whiteEnPassantPos(1) Then
                pieceArr(whiteEnPassantPos(0) + 1, whiteEnPassantPos(1)) = ""
            End If
            If pieceString = "♟︎" And ypos = blackEnPassantPos(0) And xpos = blackEnPassantPos(1) Then
                pieceArr(blackEnPassantPos(0) - 1, blackEnPassantPos(1)) = ""
            End If


            pieceArr(ypos, xpos) = pieceString
            pieceArr(lastypos, lastxpos) = ""

            If whiteTurn Then
                whiteTurn = False
            Else
                whiteTurn = True
            End If

        ElseIf pieceArr(ypos, xpos) = "" Then
            For i = 0 To 7
                For j = 0 To 7
                    If highlightArr(i, j) = "H" Then
                        highlightArr(i, j) = ""
                    End If
                Next
            Next


        Else
            'HIGHLIGHT PIECES
            Dim count As Integer
            Dim hitPiece As Boolean
            If whiteTurn And whitePieces.Contains(pieceArr(ypos, xpos)) Then
                'WHITE PAWN
                If pieceArr(ypos, xpos) = "♙" Then
                    For i = 0 To 7
                        For j = 0 To 7
                            If highlightArr(i, j) = "H" Then
                                highlightArr(i, j) = ""
                            End If
                        Next
                    Next
                    Try
                        If pieceArr(ypos - 1, xpos) = "" Then
                            highlightArr(ypos - 1, xpos) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If ypos = 6 And pieceArr(ypos - 2, xpos) = "" Then
                            highlightArr(ypos - 2, xpos) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If blackPieces.Contains(pieceArr(ypos - 1, xpos + 1)) Then
                            highlightArr(ypos - 1, xpos + 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If blackPieces.Contains(pieceArr(ypos - 1, xpos - 1)) Then
                            highlightArr(ypos - 1, xpos - 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    If whiteEnPassantPieceList.Count > 0 Then
                        For i = 0 To whiteEnPassantPieceList.Count - 1
                            If ypos = whiteEnPassantPieceList(i)(0) And xpos = whiteEnPassantPieceList(i)(1) Then
                                highlightArr(whiteEnPassantPos(0), whiteEnPassantPos(1)) = "H"
                            End If
                        Next
                    End If

                End If

                'WHITE KNIGHT
                If pieceArr(ypos, xpos) = "♘" Then
                    For i = 0 To 7
                        For j = 0 To 7
                            If highlightArr(i, j) = "H" Then
                                highlightArr(i, j) = ""
                            End If
                        Next
                    Next
                    Try
                        If pieceArr(ypos - 2, xpos - 1) = "" Or blackPieces.Contains(pieceArr(ypos - 2, xpos - 1)) Then
                            highlightArr(ypos - 2, xpos - 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos - 2, xpos + 1) = "" Or blackPieces.Contains(pieceArr(ypos - 2, xpos + 1)) Then
                            highlightArr(ypos - 2, xpos + 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos + 2, xpos - 1) = "" Or blackPieces.Contains(pieceArr(ypos + 2, xpos - 1)) Then
                            highlightArr(ypos + 2, xpos - 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos + 2, xpos + 1) = "" Or blackPieces.Contains(pieceArr(ypos + 2, xpos + 1)) Then
                            highlightArr(ypos + 2, xpos + 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos - 1, xpos - 2) = "" Or blackPieces.Contains(pieceArr(ypos - 1, xpos - 2)) Then
                            highlightArr(ypos - 1, xpos - 2) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos - 1, xpos + 2) = "" Or blackPieces.Contains(pieceArr(ypos - 1, xpos + 2)) Then
                            highlightArr(ypos - 1, xpos + 2) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos + 1, xpos - 2) = "" Or blackPieces.Contains(pieceArr(ypos + 1, xpos - 2)) Then
                            highlightArr(ypos + 1, xpos - 2) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos + 1, xpos + 2) = "" Or blackPieces.Contains(pieceArr(ypos + 1, xpos + 2)) Then
                            highlightArr(ypos + 1, xpos + 2) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                End If

                'WHITE BISHOP

                If pieceArr(ypos, xpos) = "♗" Then
                    For i = 0 To 7
                        For j = 0 To 7
                            If highlightArr(i, j) = "H" Then
                                highlightArr(i, j) = ""
                            End If
                        Next
                    Next
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos + count) = "" Or blackPieces.Contains(pieceArr(ypos + count, xpos + count))) And Not hitPiece
                            highlightArr(ypos + count, xpos + count) = "H"
                            If blackPieces.Contains(pieceArr(ypos + count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos - count) = "" Or blackPieces.Contains(pieceArr(ypos + count, xpos - count))) And Not hitPiece
                            highlightArr(ypos + count, xpos - count) = "H"
                            If blackPieces.Contains(pieceArr(ypos + count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos + count) = "" Or blackPieces.Contains(pieceArr(ypos - count, xpos + count))) And Not hitPiece
                            highlightArr(ypos - count, xpos + count) = "H"
                            If blackPieces.Contains(pieceArr(ypos - count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos - count) = "" Or blackPieces.Contains(pieceArr(ypos - count, xpos - count))) And Not hitPiece
                            highlightArr(ypos - count, xpos - count) = "H"
                            If blackPieces.Contains(pieceArr(ypos - count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                End If


                'WHITE ROOK
                If pieceArr(ypos, xpos) = "♖" Then
                    For i = 0 To 7
                        For j = 0 To 7
                            If highlightArr(i, j) = "H" Then
                                highlightArr(i, j) = ""
                            End If
                        Next
                    Next
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos + count) = "" Or blackPieces.Contains(pieceArr(ypos, xpos + count))) And Not hitPiece
                            highlightArr(ypos, xpos + count) = "H"
                            If blackPieces.Contains(pieceArr(ypos, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos - count) = "" Or blackPieces.Contains(pieceArr(ypos, xpos - count))) And Not hitPiece
                            highlightArr(ypos, xpos - count) = "H"
                            If blackPieces.Contains(pieceArr(ypos, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos) = "" Or blackPieces.Contains(pieceArr(ypos - count, xpos))) And Not hitPiece
                            highlightArr(ypos - count, xpos) = "H"
                            If blackPieces.Contains(pieceArr(ypos - count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos) = "" Or blackPieces.Contains(pieceArr(ypos + count, xpos))) And Not hitPiece
                            highlightArr(ypos + count, xpos) = "H"
                            If blackPieces.Contains(pieceArr(ypos + count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                End If


                'WHITE QUEEN
                If pieceArr(ypos, xpos) = "♕" Then
                    For i = 0 To 7
                        For j = 0 To 7
                            If highlightArr(i, j) = "H" Then
                                highlightArr(i, j) = ""
                            End If
                        Next
                    Next
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos + count) = "" Or blackPieces.Contains(pieceArr(ypos + count, xpos + count))) And Not hitPiece
                            highlightArr(ypos + count, xpos + count) = "H"
                            If blackPieces.Contains(pieceArr(ypos + count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos - count) = "" Or blackPieces.Contains(pieceArr(ypos + count, xpos - count))) And Not hitPiece
                            highlightArr(ypos + count, xpos - count) = "H"
                            If blackPieces.Contains(pieceArr(ypos + count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos + count) = "" Or blackPieces.Contains(pieceArr(ypos - count, xpos + count))) And Not hitPiece
                            highlightArr(ypos - count, xpos + count) = "H"
                            If blackPieces.Contains(pieceArr(ypos - count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos - count) = "" Or blackPieces.Contains(pieceArr(ypos - count, xpos - count))) And Not hitPiece
                            highlightArr(ypos - count, xpos - count) = "H"
                            If blackPieces.Contains(pieceArr(ypos - count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos + count) = "" Or blackPieces.Contains(pieceArr(ypos, xpos + count))) And Not hitPiece
                            highlightArr(ypos, xpos + count) = "H"
                            If blackPieces.Contains(pieceArr(ypos, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos - count) = "" Or blackPieces.Contains(pieceArr(ypos, xpos - count))) And Not hitPiece
                            highlightArr(ypos, xpos - count) = "H"
                            If blackPieces.Contains(pieceArr(ypos, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos) = "" Or blackPieces.Contains(pieceArr(ypos - count, xpos))) And Not hitPiece
                            highlightArr(ypos - count, xpos) = "H"
                            If blackPieces.Contains(pieceArr(ypos - count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos) = "" Or blackPieces.Contains(pieceArr(ypos + count, xpos))) And Not hitPiece
                            highlightArr(ypos + count, xpos) = "H"
                            If blackPieces.Contains(pieceArr(ypos + count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                End If


                'WHITE KING
                If pieceArr(ypos, xpos) = "♔" Then
                    For i = 0 To 7
                        For j = 0 To 7
                            If highlightArr(i, j) = "H" Then
                                highlightArr(i, j) = ""
                            End If
                        Next
                    Next

                    Try
                        If (pieceArr(ypos + 1, xpos) = "" Or blackPieces.Contains(pieceArr(ypos + 1, xpos))) And Not blackAttackArr(ypos + 1, xpos) Then
                            highlightArr(ypos + 1, xpos) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos + 1, xpos + 1) = "" Or blackPieces.Contains(pieceArr(ypos + 1, xpos + 1))) And Not blackAttackArr(ypos + 1, xpos + 1) Then
                            highlightArr(ypos + 1, xpos + 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos + 1, xpos - 1) = "" Or blackPieces.Contains(pieceArr(ypos + 1, xpos - 1))) And Not blackAttackArr(ypos + 1, xpos - 1) Then
                            highlightArr(ypos + 1, xpos - 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos - 1, xpos) = "" Or blackPieces.Contains(pieceArr(ypos - 1, xpos))) And Not blackAttackArr(ypos - 1, xpos) Then
                            highlightArr(ypos - 1, xpos) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos - 1, xpos + 1) = "" Or blackPieces.Contains(pieceArr(ypos - 1, xpos + 1))) And Not blackAttackArr(ypos - 1, xpos + 1) Then
                            highlightArr(ypos - 1, xpos + 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos - 1, xpos - 1) = "" Or blackPieces.Contains(pieceArr(ypos - 1, xpos - 1))) And Not blackAttackArr(ypos - 1, xpos - 1) Then
                            highlightArr(ypos - 1, xpos - 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos, xpos + 1) = "" Or blackPieces.Contains(pieceArr(ypos, xpos + 1))) And Not blackAttackArr(ypos, xpos + 1) Then
                            highlightArr(ypos, xpos + 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos, xpos - 1) = "" Or blackPieces.Contains(pieceArr(ypos, xpos - 1))) And Not blackAttackArr(ypos, xpos - 1) Then
                            highlightArr(ypos, xpos - 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    If pieceArr(7, 1) = "" And blackAttackArr(7, 1) = False And pieceArr(7, 2) = "" And blackAttackArr(7, 2) = False And pieceArr(7, 3) = "" And blackAttackArr(7, 3) = False And whiteCanCastleQueen Then
                        highlightArr(7, 2) = "H"
                    End If
                    If pieceArr(7, 5) = "" And blackAttackArr(7, 5) = False And pieceArr(7, 6) = "" And blackAttackArr(7, 6) = False And whiteCanCastleKing Then
                        highlightArr(7, 6) = "H"
                    End If

                End If


            End If


            If Not whiteTurn And blackPieces.Contains(pieceArr(ypos, xpos)) Then

                'BLACK PAWN
                If pieceArr(ypos, xpos) = "♟︎" Then
                    For i = 0 To 7
                        For j = 0 To 7
                            If highlightArr(i, j) = "H" Then
                                highlightArr(i, j) = ""
                            End If
                        Next
                    Next

                    Try
                        If pieceArr(ypos + 1, xpos) = "" Then
                            highlightArr(ypos + 1, xpos) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If ypos = 1 And pieceArr(ypos + 2, xpos) = "" Then
                            highlightArr(ypos + 2, xpos) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If whitePieces.Contains(pieceArr(ypos + 1, xpos + 1)) Then
                            highlightArr(ypos + 1, xpos + 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If whitePieces.Contains(pieceArr(ypos + 1, xpos - 1)) Then
                            highlightArr(ypos + 1, xpos - 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    If blackEnPassantPieceList.Count > 0 Then
                        For i = 0 To blackEnPassantPieceList.Count - 1
                            If ypos = blackEnPassantPieceList(i)(0) And xpos = blackEnPassantPieceList(i)(1) Then
                                highlightArr(blackEnPassantPos(0), blackEnPassantPos(1)) = "H"
                            End If
                        Next
                    End If

                End If


                'BLACK KNIGHT
                If pieceArr(ypos, xpos) = "♞" Then
                    For i = 0 To 7
                        For j = 0 To 7
                            If highlightArr(i, j) = "H" Then
                                highlightArr(i, j) = ""
                            End If
                        Next
                    Next
                    Try
                        If pieceArr(ypos - 2, xpos - 1) = "" Or whitePieces.Contains(pieceArr(ypos - 2, xpos - 1)) Then
                            highlightArr(ypos - 2, xpos - 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos - 2, xpos + 1) = "" Or whitePieces.Contains(pieceArr(ypos - 2, xpos + 1)) Then
                            highlightArr(ypos - 2, xpos + 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos + 2, xpos - 1) = "" Or whitePieces.Contains(pieceArr(ypos + 2, xpos - 1)) Then
                            highlightArr(ypos + 2, xpos - 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos + 2, xpos + 1) = "" Or whitePieces.Contains(pieceArr(ypos + 2, xpos + 1)) Then
                            highlightArr(ypos + 2, xpos + 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos - 1, xpos - 2) = "" Or whitePieces.Contains(pieceArr(ypos - 1, xpos - 2)) Then
                            highlightArr(ypos - 1, xpos - 2) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos - 1, xpos + 2) = "" Or whitePieces.Contains(pieceArr(ypos - 1, xpos + 2)) Then
                            highlightArr(ypos - 1, xpos + 2) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos + 1, xpos - 2) = "" Or whitePieces.Contains(pieceArr(ypos + 1, xpos - 2)) Then
                            highlightArr(ypos + 1, xpos - 2) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    Try
                        If pieceArr(ypos + 1, xpos + 2) = "" Or whitePieces.Contains(pieceArr(ypos + 1, xpos + 2)) Then
                            highlightArr(ypos + 1, xpos + 2) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                End If




                'BLACK BISHOP
                If pieceArr(ypos, xpos) = "♝" Then
                    For i = 0 To 7
                        For j = 0 To 7
                            If highlightArr(i, j) = "H" Then
                                highlightArr(i, j) = ""
                            End If
                        Next
                    Next
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos + count) = "" Or whitePieces.Contains(pieceArr(ypos + count, xpos + count))) And Not hitPiece
                            highlightArr(ypos + count, xpos + count) = "H"
                            If whitePieces.Contains(pieceArr(ypos + count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos - count) = "" Or whitePieces.Contains(pieceArr(ypos + count, xpos - count))) And Not hitPiece
                            highlightArr(ypos + count, xpos - count) = "H"
                            If whitePieces.Contains(pieceArr(ypos + count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos + count) = "" Or whitePieces.Contains(pieceArr(ypos - count, xpos + count))) And Not hitPiece
                            highlightArr(ypos - count, xpos + count) = "H"
                            If whitePieces.Contains(pieceArr(ypos - count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos - count) = "" Or whitePieces.Contains(pieceArr(ypos - count, xpos - count))) And Not hitPiece
                            highlightArr(ypos - count, xpos - count) = "H"
                            If whitePieces.Contains(pieceArr(ypos - count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try


                End If



                'BLACK ROOK
                If pieceArr(ypos, xpos) = "♜" Then
                    For i = 0 To 7
                        For j = 0 To 7
                            If highlightArr(i, j) = "H" Then
                                highlightArr(i, j) = ""
                            End If
                        Next
                    Next
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos + count) = "" Or whitePieces.Contains(pieceArr(ypos, xpos + count))) And Not hitPiece
                            highlightArr(ypos, xpos + count) = "H"
                            If whitePieces.Contains(pieceArr(ypos, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos - count) = "" Or whitePieces.Contains(pieceArr(ypos, xpos - count))) And Not hitPiece
                            highlightArr(ypos, xpos - count) = "H"
                            If whitePieces.Contains(pieceArr(ypos, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos) = "" Or whitePieces.Contains(pieceArr(ypos - count, xpos))) And Not hitPiece
                            highlightArr(ypos - count, xpos) = "H"
                            If whitePieces.Contains(pieceArr(ypos - count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos) = "" Or whitePieces.Contains(pieceArr(ypos + count, xpos))) And Not hitPiece
                            highlightArr(ypos + count, xpos) = "H"
                            If whitePieces.Contains(pieceArr(ypos + count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                End If





                'BLACK QUEEN
                If pieceArr(ypos, xpos) = "♛" Then
                    For i = 0 To 7
                        For j = 0 To 7
                            If highlightArr(i, j) = "H" Then
                                highlightArr(i, j) = ""
                            End If
                        Next
                    Next
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos + count) = "" Or whitePieces.Contains(pieceArr(ypos + count, xpos + count))) And Not hitPiece
                            highlightArr(ypos + count, xpos + count) = "H"
                            If whitePieces.Contains(pieceArr(ypos + count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception
                    End Try
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos - count) = "" Or whitePieces.Contains(pieceArr(ypos + count, xpos - count))) And Not hitPiece
                            highlightArr(ypos + count, xpos - count) = "H"
                            If whitePieces.Contains(pieceArr(ypos + count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception
                    End Try
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos + count) = "" Or whitePieces.Contains(pieceArr(ypos - count, xpos + count))) And Not hitPiece
                            highlightArr(ypos - count, xpos + count) = "H"
                            If whitePieces.Contains(pieceArr(ypos - count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception
                    End Try
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos - count) = "" Or whitePieces.Contains(pieceArr(ypos - count, xpos - count))) And Not hitPiece
                            highlightArr(ypos - count, xpos - count) = "H"
                            If whitePieces.Contains(pieceArr(ypos - count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos + count) = "" Or whitePieces.Contains(pieceArr(ypos, xpos + count))) And Not hitPiece
                            highlightArr(ypos, xpos + count) = "H"
                            If whitePieces.Contains(pieceArr(ypos, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos - count) = "" Or whitePieces.Contains(pieceArr(ypos, xpos - count))) And Not hitPiece
                            highlightArr(ypos, xpos - count) = "H"
                            If whitePieces.Contains(pieceArr(ypos, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos) = "" Or whitePieces.Contains(pieceArr(ypos - count, xpos))) And Not hitPiece
                            highlightArr(ypos - count, xpos) = "H"
                            If whitePieces.Contains(pieceArr(ypos - count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos) = "" Or whitePieces.Contains(pieceArr(ypos + count, xpos))) And Not hitPiece
                            highlightArr(ypos + count, xpos) = "H"
                            If whitePieces.Contains(pieceArr(ypos + count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                End If




                'BLACK KING
                If pieceArr(ypos, xpos) = "♚" Then
                    For i = 0 To 7
                        For j = 0 To 7
                            If highlightArr(i, j) = "H" Then
                                highlightArr(i, j) = ""
                            End If
                        Next
                    Next

                    Try
                        If (pieceArr(ypos + 1, xpos) = "" Or whitePieces.Contains(pieceArr(ypos + 1, xpos))) And Not whiteAttackArr(ypos + 1, xpos) Then
                            highlightArr(ypos + 1, xpos) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos + 1, xpos + 1) = "" Or whitePieces.Contains(pieceArr(ypos + 1, xpos + 1))) And Not whiteAttackArr(ypos + 1, xpos + 1) Then
                            highlightArr(ypos + 1, xpos + 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos + 1, xpos - 1) = "" Or whitePieces.Contains(pieceArr(ypos + 1, xpos - 1))) And Not whiteAttackArr(ypos + 1, xpos - 1) Then
                            highlightArr(ypos + 1, xpos - 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos - 1, xpos) = "" Or whitePieces.Contains(pieceArr(ypos - 1, xpos))) And Not whiteAttackArr(ypos - 1, xpos) Then
                            highlightArr(ypos - 1, xpos) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos - 1, xpos + 1) = "" Or whitePieces.Contains(pieceArr(ypos - 1, xpos + 1))) And Not whiteAttackArr(ypos - 1, xpos + 1) Then
                            highlightArr(ypos - 1, xpos + 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos - 1, xpos - 1) = "" Or whitePieces.Contains(pieceArr(ypos - 1, xpos - 1))) And Not whiteAttackArr(ypos - 1, xpos - 1) Then
                            highlightArr(ypos - 1, xpos - 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos, xpos + 1) = "" Or whitePieces.Contains(pieceArr(ypos, xpos + 1))) And Not whiteAttackArr(ypos, xpos + 1) Then
                            highlightArr(ypos, xpos + 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos, xpos - 1) = "" Or whitePieces.Contains(pieceArr(ypos, xpos - 1))) And Not whiteAttackArr(ypos, xpos - 1) Then
                            highlightArr(ypos, xpos - 1) = "H"
                        End If
                    Catch ex As Exception

                    End Try

                    If pieceArr(0, 1) = "" And whiteAttackArr(0, 1) = False And pieceArr(0, 2) = "" And whiteAttackArr(0, 2) = False And pieceArr(0, 3) = "" And whiteAttackArr(0, 3) = False And blackCanCastleQueen Then
                        highlightArr(0, 2) = "H"
                    End If
                    If pieceArr(0, 5) = "" And whiteAttackArr(0, 5) = False And pieceArr(0, 6) = "" And whiteAttackArr(0, 6) = False And blackCanCastleKing Then
                        highlightArr(0, 6) = "H"
                    End If

                End If

            End If
        End If



        lastPieceClicked = sender.Name
        updateBoard(Controls("board"))


    End Sub
    Sub updateBoard(board As TableLayoutPanel)

        '        Dim boardString As String
        '        For i = 0 To 7
        '            For j = 0 To 7
        '                If pieceArr(i, j) = "" Then
        '                    boardString &= "O"
        '                Else
        '                    boardString &= pieceArr(i, j)
        '                End If

        '            Next
        '            boardString &= "
        '"
        '        Next
        '        MessageBox.Show(boardString)

        Dim count As Integer = 0
        For i = 0 To 7
            For j = 0 To 7
                If highlightArr(i, j) = ("H") Then
                    board.Controls(count).BackColor = Color.Red
                Else
                    board.Controls(count).BackColor = DefaultBackColor
                    board.Controls(count).Text = pieceArr(i, j)
                End If
                count += 1
            Next

        Next

        For i = 0 To 7
            For j = 0 To 7
                blackAttackArr(i, j) = False
                whiteAttackArr(i, j) = False
            Next
        Next


        For xpos = 0 To 7
            For ypos = 0 To 7
                'WHITE PAWN
                If pieceArr(ypos, xpos) = "♙" Then
                    Try
                        whiteAttackArr(ypos - 1, xpos + 1) = True
                    Catch ex As Exception

                    End Try

                    Try
                        whiteAttackArr(ypos - 1, xpos - 1) = True
                    Catch ex As Exception

                    End Try
                End If

                'BLACK PAWN
                If pieceArr(ypos, xpos) = "♟︎" Then
                    Try
                        blackAttackArr(ypos + 1, xpos + 1) = True
                    Catch ex As Exception

                    End Try

                    Try
                        blackAttackArr(ypos + 1, xpos - 1) = True
                    Catch ex As Exception

                    End Try
                End If

                'WHITE KNIGHT
                If pieceArr(ypos, xpos) = "♘" Then
                    Try
                        whiteAttackArr(ypos - 2, xpos - 1) = True
                    Catch ex As Exception

                    End Try

                    Try
                        whiteAttackArr(ypos - 2, xpos + 1) = True
                    Catch ex As Exception

                    End Try

                    Try
                        whiteAttackArr(ypos + 2, xpos - 1) = True
                    Catch ex As Exception

                    End Try

                    Try
                        whiteAttackArr(ypos + 2, xpos + 1) = True
                    Catch ex As Exception

                    End Try

                    Try
                        whiteAttackArr(ypos - 1, xpos - 2) = True
                    Catch ex As Exception

                    End Try

                    Try
                        whiteAttackArr(ypos - 1, xpos + 2) = True
                    Catch ex As Exception

                    End Try

                    Try
                        whiteAttackArr(ypos + 1, xpos - 2) = True
                    Catch ex As Exception

                    End Try

                    Try
                        whiteAttackArr(ypos + 1, xpos + 2) = True
                    Catch ex As Exception

                    End Try

                End If

                'BLACK KNIGHT
                If pieceArr(ypos, xpos) = "♞" Then
                    Try
                        blackAttackArr(ypos - 2, xpos - 1) = True
                    Catch ex As Exception

                    End Try

                    Try
                        blackAttackArr(ypos - 2, xpos + 1) = True
                    Catch ex As Exception

                    End Try

                    Try
                        blackAttackArr(ypos + 2, xpos - 1) = True
                    Catch ex As Exception

                    End Try

                    Try
                        blackAttackArr(ypos + 2, xpos + 1) = True
                    Catch ex As Exception

                    End Try

                    Try
                        blackAttackArr(ypos - 1, xpos - 2) = True
                    Catch ex As Exception

                    End Try

                    Try
                        blackAttackArr(ypos - 1, xpos + 2) = True
                    Catch ex As Exception

                    End Try

                    Try
                        blackAttackArr(ypos + 1, xpos - 2) = True
                    Catch ex As Exception

                    End Try

                    Try
                        blackAttackArr(ypos + 1, xpos + 2) = True
                    Catch ex As Exception

                    End Try

                End If

                'WHITE BISHOP
                Dim hitPiece As Boolean
                If pieceArr(ypos, xpos) = "♗" Then

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos + count) = "" Or blackPieces.Contains(pieceArr(ypos + count, xpos + count))) And Not hitPiece
                            whiteAttackArr(ypos + count, xpos + count) = True
                            If blackPieces.Contains(pieceArr(ypos + count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos - count) = "" Or blackPieces.Contains(pieceArr(ypos + count, xpos - count))) And Not hitPiece
                            whiteAttackArr(ypos + count, xpos - count) = True
                            If blackPieces.Contains(pieceArr(ypos + count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos + count) = "" Or blackPieces.Contains(pieceArr(ypos - count, xpos + count))) And Not hitPiece
                            whiteAttackArr(ypos - count, xpos + count) = True
                            If blackPieces.Contains(pieceArr(ypos - count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos - count) = "" Or blackPieces.Contains(pieceArr(ypos - count, xpos - count))) And Not hitPiece
                            whiteAttackArr(ypos - count, xpos - count) = True
                            If blackPieces.Contains(pieceArr(ypos - count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                End If


                'BLACK BISHOP
                If pieceArr(ypos, xpos) = "♝" Then

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos + count) = "" Or whitePieces.Contains(pieceArr(ypos + count, xpos + count))) And Not hitPiece
                            blackAttackArr(ypos + count, xpos + count) = True
                            If whitePieces.Contains(pieceArr(ypos + count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos - count) = "" Or whitePieces.Contains(pieceArr(ypos + count, xpos - count))) And Not hitPiece
                            blackAttackArr(ypos + count, xpos - count) = True
                            If whitePieces.Contains(pieceArr(ypos + count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos + count) = "" Or whitePieces.Contains(pieceArr(ypos - count, xpos + count))) And Not hitPiece
                            blackAttackArr(ypos - count, xpos + count) = True
                            If whitePieces.Contains(pieceArr(ypos - count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos - count) = "" Or whitePieces.Contains(pieceArr(ypos - count, xpos - count))) And Not hitPiece
                            blackAttackArr(ypos - count, xpos - count) = True
                            If whitePieces.Contains(pieceArr(ypos - count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                End If

                'WHITE ROOK
                If pieceArr(ypos, xpos) = "♖" Then
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos + count) = "" Or blackPieces.Contains(pieceArr(ypos, xpos + count))) And Not hitPiece
                            whiteAttackArr(ypos, xpos + count) = True
                            If blackPieces.Contains(pieceArr(ypos, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos - count) = "" Or blackPieces.Contains(pieceArr(ypos, xpos - count))) And Not hitPiece
                            whiteAttackArr(ypos, xpos - count) = True
                            If blackPieces.Contains(pieceArr(ypos, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos) = "" Or blackPieces.Contains(pieceArr(ypos - count, xpos))) And Not hitPiece
                            whiteAttackArr(ypos - count, xpos) = True
                            If blackPieces.Contains(pieceArr(ypos - count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos) = "" Or blackPieces.Contains(pieceArr(ypos + count, xpos))) And Not hitPiece
                            whiteAttackArr(ypos + count, xpos) = True
                            If blackPieces.Contains(pieceArr(ypos + count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                End If


                'BLACK ROOK
                If pieceArr(ypos, xpos) = "♜" Then
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos + count) = "" Or whitePieces.Contains(pieceArr(ypos, xpos + count))) And Not hitPiece
                            blackAttackArr(ypos, xpos + count) = True
                            If whitePieces.Contains(pieceArr(ypos, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos - count) = "" Or whitePieces.Contains(pieceArr(ypos, xpos - count))) And Not hitPiece
                            blackAttackArr(ypos, xpos - count) = True
                            If whitePieces.Contains(pieceArr(ypos, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos) = "" Or whitePieces.Contains(pieceArr(ypos - count, xpos))) And Not hitPiece
                            blackAttackArr(ypos - count, xpos) = True
                            If whitePieces.Contains(pieceArr(ypos - count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos) = "" Or whitePieces.Contains(pieceArr(ypos + count, xpos))) And Not hitPiece
                            blackAttackArr(ypos + count, xpos) = True
                            If whitePieces.Contains(pieceArr(ypos + count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                End If

                'WHITE QUEEN
                If pieceArr(ypos, xpos) = "♕" Then
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos + count) = "" Or blackPieces.Contains(pieceArr(ypos + count, xpos + count))) And Not hitPiece
                            whiteAttackArr(ypos + count, xpos + count) = True
                            If blackPieces.Contains(pieceArr(ypos + count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos - count) = "" Or blackPieces.Contains(pieceArr(ypos + count, xpos - count))) And Not hitPiece
                            whiteAttackArr(ypos + count, xpos - count) = True
                            If blackPieces.Contains(pieceArr(ypos + count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos + count) = "" Or blackPieces.Contains(pieceArr(ypos - count, xpos + count))) And Not hitPiece
                            whiteAttackArr(ypos - count, xpos + count) = True
                            If blackPieces.Contains(pieceArr(ypos - count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos - count) = "" Or blackPieces.Contains(pieceArr(ypos - count, xpos - count))) And Not hitPiece
                            whiteAttackArr(ypos - count, xpos - count) = True
                            If blackPieces.Contains(pieceArr(ypos - count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos + count) = "" Or blackPieces.Contains(pieceArr(ypos, xpos + count))) And Not hitPiece
                            whiteAttackArr(ypos, xpos + count) = True
                            If blackPieces.Contains(pieceArr(ypos, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos - count) = "" Or blackPieces.Contains(pieceArr(ypos, xpos - count))) And Not hitPiece
                            whiteAttackArr(ypos, xpos - count) = True
                            If blackPieces.Contains(pieceArr(ypos, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos) = "" Or blackPieces.Contains(pieceArr(ypos - count, xpos))) And Not hitPiece
                            whiteAttackArr(ypos - count, xpos) = True
                            If blackPieces.Contains(pieceArr(ypos - count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos) = "" Or blackPieces.Contains(pieceArr(ypos + count, xpos))) And Not hitPiece
                            whiteAttackArr(ypos + count, xpos) = True
                            If blackPieces.Contains(pieceArr(ypos + count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                End If





                'BLACK QUEEN
                If pieceArr(ypos, xpos) = "♛" Then
                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos + count) = "" Or whitePieces.Contains(pieceArr(ypos + count, xpos + count))) And Not hitPiece
                            blackAttackArr(ypos + count, xpos + count) = True
                            If whitePieces.Contains(pieceArr(ypos + count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos - count) = "" Or whitePieces.Contains(pieceArr(ypos + count, xpos - count))) And Not hitPiece
                            blackAttackArr(ypos + count, xpos - count) = True
                            If whitePieces.Contains(pieceArr(ypos + count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos + count) = "" Or whitePieces.Contains(pieceArr(ypos - count, xpos + count))) And Not hitPiece
                            blackAttackArr(ypos - count, xpos + count) = True
                            If whitePieces.Contains(pieceArr(ypos - count, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos - count) = "" Or whitePieces.Contains(pieceArr(ypos - count, xpos - count))) And Not hitPiece
                            blackAttackArr(ypos - count, xpos - count) = True
                            If whitePieces.Contains(pieceArr(ypos - count, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos + count) = "" Or whitePieces.Contains(pieceArr(ypos, xpos + count))) And Not hitPiece
                            blackAttackArr(ypos, xpos + count) = True
                            If whitePieces.Contains(pieceArr(ypos, xpos + count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos, xpos - count) = "" Or whitePieces.Contains(pieceArr(ypos, xpos - count))) And Not hitPiece
                            blackAttackArr(ypos, xpos - count) = True
                            If whitePieces.Contains(pieceArr(ypos, xpos - count)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos - count, xpos) = "" Or whitePieces.Contains(pieceArr(ypos - count, xpos))) And Not hitPiece
                            blackAttackArr(ypos - count, xpos) = True
                            If whitePieces.Contains(pieceArr(ypos - count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try

                    Try
                        count = 1
                        hitPiece = False
                        While (pieceArr(ypos + count, xpos) = "" Or whitePieces.Contains(pieceArr(ypos + count, xpos))) And Not hitPiece
                            blackAttackArr(ypos + count, xpos) = True
                            If whitePieces.Contains(pieceArr(ypos + count, xpos)) Then
                                hitPiece = True
                            End If
                            count += 1
                        End While
                    Catch ex As Exception

                    End Try
                End If

                'WHITE KING
                If pieceArr(ypos, xpos) = "♔" Then

                    Try
                        If (pieceArr(ypos + 1, xpos) = "" Or blackPieces.Contains(pieceArr(ypos + 1, xpos))) Then
                            whiteAttackArr(ypos + 1, xpos) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos + 1, xpos + 1) = "" Or blackPieces.Contains(pieceArr(ypos + 1, xpos + 1))) Then
                            whiteAttackArr(ypos + 1, xpos + 1) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos + 1, xpos - 1) = "" Or blackPieces.Contains(pieceArr(ypos + 1, xpos - 1))) Then
                            whiteAttackArr(ypos + 1, xpos - 1) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos - 1, xpos) = "" Or blackPieces.Contains(pieceArr(ypos - 1, xpos))) Then
                            whiteAttackArr(ypos - 1, xpos) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos - 1, xpos + 1) = "" Or blackPieces.Contains(pieceArr(ypos - 1, xpos + 1))) Then
                            whiteAttackArr(ypos - 1, xpos + 1) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos - 1, xpos - 1) = "" Or blackPieces.Contains(pieceArr(ypos - 1, xpos - 1))) Then
                            whiteAttackArr(ypos - 1, xpos - 1) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos, xpos + 1) = "" Or blackPieces.Contains(pieceArr(ypos, xpos + 1))) Then
                            whiteAttackArr(ypos, xpos + 1) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos, xpos - 1) = "" Or blackPieces.Contains(pieceArr(ypos, xpos - 1))) Then
                            whiteAttackArr(ypos, xpos - 1) = True
                        End If
                    Catch ex As Exception

                    End Try
                End If


                'BLACK KING
                If pieceArr(ypos, xpos) = "♚" Then


                    Try
                        If (pieceArr(ypos + 1, xpos) = "" Or whitePieces.Contains(pieceArr(ypos + 1, xpos))) Then
                            blackAttackArr(ypos + 1, xpos) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos + 1, xpos + 1) = "" Or whitePieces.Contains(pieceArr(ypos + 1, xpos + 1))) Then
                            blackAttackArr(ypos + 1, xpos + 1) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos + 1, xpos - 1) = "" Or whitePieces.Contains(pieceArr(ypos + 1, xpos - 1))) Then
                            blackAttackArr(ypos + 1, xpos - 1) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos - 1, xpos) = "" Or whitePieces.Contains(pieceArr(ypos - 1, xpos))) Then
                            blackAttackArr(ypos - 1, xpos) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos - 1, xpos + 1) = "" Or whitePieces.Contains(pieceArr(ypos - 1, xpos + 1))) Then
                            blackAttackArr(ypos - 1, xpos + 1) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos - 1, xpos - 1) = "" Or whitePieces.Contains(pieceArr(ypos - 1, xpos - 1))) Then
                            blackAttackArr(ypos - 1, xpos - 1) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos, xpos + 1) = "" Or whitePieces.Contains(pieceArr(ypos, xpos + 1))) Then
                            blackAttackArr(ypos, xpos + 1) = True
                        End If
                    Catch ex As Exception

                    End Try
                    Try
                        If (pieceArr(ypos, xpos - 1) = "" Or whitePieces.Contains(pieceArr(ypos, xpos - 1))) Then
                            blackAttackArr(ypos, xpos - 1) = True
                        End If
                    Catch ex As Exception

                    End Try
                End If

            Next
        Next

        'count = 0
        'For i = 0 To 7
        '    For j = 0 To 7
        '        If whiteAttackArr(i, j) Then
        '            board.Controls(count).BackColor = Color.Green
        '        End If
        '        count += 1
        '    Next
        'Next

    End Sub

End Class
