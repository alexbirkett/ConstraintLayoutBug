# ConstraintLayoutBug
Demonstrates a possible bug in the ConstraintLayout class

[![Watch on Youtube](http://img.youtube.com/vi/r5AgejfA0O0/0.jpg)](http://www.youtube.com/watch?v=r5AgejfA0O0 "ConstraintLayout bug?")

[Watch on Youtube](http://www.youtube.com/watch?v=r5AgejfA0O0)


`ConstraintLayout` intermittently fails to layout correctly when a view is set from `GONE` to `VISIBLE` shortly after an activity is resumed:

    <android.support.constraint.ConstraintLayout
        android:id="@+id/constraint_layout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
    
        <TextView
            android:id="@+id/text1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintTop_toTopOf="parent"/>
    
        <TextView
            android:id="@+id/text2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:layout_constraintLeft_toRightOf="@id/text1"
            app:layout_constraintTop_toTopOf="parent"/>
    </android.support.constraint.ConstraintLayout>



    override fun onResume() {
            super.onResume()
    
            text1.text = ""
            text1.visibility = View.GONE
    
            text2.text = ""
            text2.visibility = View.GONE
    
            text1.postDelayed({
                        text1.text = "Hello"
                        text1.visibility = View.VISIBLE
    
                        text2.text = "World"
                        text2.visibility = View.VISIBLE
                    }, 100
            )
        }

Instrumenting the `TextView` class reveals that the `TextView` instances are measured correctly but their width is set to 0 when they are laid out.
